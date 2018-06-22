---
title: Invocación de eventos de efectos
description: Un efecto puede definir e invocar un evento, cambios en la vista nativo subyacente de señalización. Este artículo muestra cómo implementar el dedo de multitoque bajo nivel de seguimiento y cómo se generan eventos que indican la actividad de entrada táctil.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: e363cae4dd72a25e4768395410d4e56a8db30eba
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34050161"
---
# <a name="invoking-events-from-effects"></a>Invocación de eventos de efectos

_Un efecto puede definir e invocar un evento, cambios en la vista nativo subyacente de señalización. Este artículo muestra cómo implementar el dedo de multitoque bajo nivel de seguimiento y cómo se generan eventos que indican la actividad de entrada táctil._

El efecto que se describe en este artículo proporciona acceso a los eventos de entrada táctil de bajo nivel. Estos eventos de bajo nivel no están disponibles a través de las existentes `GestureRecognizer` las clases, pero son vitales para algunos tipos de aplicaciones. Por ejemplo, un finger-paint necesidades de aplicación para realizar el seguimiento dedos individuales a medida que mueven en la pantalla. Un teclado de música debe detectar derivaciones y libera en las claves individuales, así como con un dedo deslizante desde una clave a otra en un glissando.

Un efecto es ideal para el seguimiento de multitoque dedo ya que puede asociarse a cualquier elemento de Xamarin.Forms.

## <a name="platform-touch-events"></a>Eventos de toque de plataforma

La plataforma Universal de Windows incluyen una API de bajo nivel que permite a las aplicaciones detectar, iOS y Android toque actividad. Pulse en estas plataformas de que todos distinguen entre tres tipos básicos eventos:

- *Presiona*, cuando un dedo toca la pantalla
- *Mover*, cuando se mueve un dedo tocar la pantalla
- *Libera*, cuando se suelta el dedo desde la pantalla

En un entorno multitoque, varios dedos pueden toque la pantalla al mismo tiempo. Las diferentes plataformas incluyen un número de identificación (ID) que las aplicaciones pueden usar para distinguir entre varios dedos.

En iOS, el `UIView` clase define tres métodos reemplazables, `TouchesBegan`, `TouchesMoved`, y `TouchesEnded` correspondientes a estos tres eventos básicos. El artículo [multitoque dedo seguimiento](~/ios/app-fundamentals/touch/touch-tracking.md) describe cómo usar estos métodos. Sin embargo, no necesita invalidar una clase que deriva de un programa de iOS `UIView` para utilizar estos métodos. El archivo iOS `UIGestureRecognizer` también define estos mismos tres métodos y se puede adjuntar una instancia de una clase que deriva de `UIGestureRecognizer` a cualquier `UIView` objeto.

En Android, el `View` clase define un método reemplazable denominado `OnTouchEvent` para procesar toda la actividad de la entrada táctil. El tipo de la actividad de entrada táctil está definido por los miembros de enumeración `Down`, `PointerDown`, `Move`, `Up`, y `PointerUp` tal y como se describe en el artículo [multitoque dedo seguimiento](~/android/app-fundamentals/touch/touch-tracking.md). El Android `View` también define un evento denominado `Touch` que permite que un controlador de eventos que se adjuntará a cualquier `View` objeto.

En la plataforma Universal de Windows (UWP), el `UIElement` clase define eventos denominados `PointerPressed`, `PointerMoved`, y `PointerReleased`. Se describen en el artículo [artículo de controlar los datos proporcionados por el puntero en MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) y la documentación de API para el [ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/) clase.

El `Pointer` API en la plataforma Universal de Windows está pensada para unificar la entrada manuscrita, toques y mouse (ratón). Por esta razón, la `PointerMoved` evento se invoca cuando el mouse se mueve sobre un elemento incluso cuando no se presiona un botón del mouse. El `PointerRoutedEventArgs` objeto que acompaña a estos eventos tiene una propiedad denominada `Pointer` que tiene una propiedad denominada `IsInContact` que indica si se presiona un botón del mouse o un dedo está en contacto con la pantalla.

Además, el UWP define dos eventos más denominados `PointerEntered` y `PointerExited`. Estos errores indican cuando un mouse o un dedo se mueve de un elemento a otro. Por ejemplo, considere dos elementos adyacentes con el nombre A y B. Ambos elementos han instalado a controladores para los eventos de puntero. Cuando se presiona un dedo en A, el `PointerPressed` se llama al evento. Cuando se desplace el dedo, invoca A `PointerMoved` eventos. Si se mueve el dedo de la A B, se invoca A un `PointerExited` eventos y B, se invoca un `PointerEntered` eventos. Si, a continuación, se libera el dedo, B, se invoca un `PointerReleased` eventos.

Las plataformas iOS y Android son diferentes de UWP: la vista que obtiene primero la llamada a `TouchesBegan` o `OnTouchEvent` cuando un dedo toca la vista continúa obtener todos los el toque actividad incluso si si se mueve el dedo vistas diferentes. UWP puede comportarse de forma similar si la aplicación captura el puntero: en el `PointerEntered` controlador de eventos, las llamadas de elemento `CapturePointer` y, a continuación, se obtiene toda la actividad táctil desde ese dedo.

El enfoque UWP resulta para ser muy útil para algunos tipos de aplicaciones, por ejemplo, un teclado de música. Cada clave puede controlar los eventos de toque para esa clave y detectar cuándo se deslizar un dedo de una clave a otro utilizando la `PointerEntered` y `PointerExited` eventos.

Por esta razón, el efecto de seguimiento de entrada táctil que se describe en este artículo implementa el enfoque UWP.

## <a name="the-touch-tracking-effect-api"></a>La API de efecto de seguimiento de entrada táctil

El [ **Touch demostraciones de efecto de seguimiento** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) ejemplo contiene las clases (y una enumeración) que implementa el seguimiento de táctil de bajo nivel. Estos tipos pertenecen al espacio de nombres `TouchTracking` y comenzar con la palabra `Touch`. El **TouchTrackingEffectDemos** proyecto de biblioteca de .NET estándar incluye el `TouchActionType` enumeración para el tipo de eventos touch:

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

Todas las plataformas incluyen también un evento que indica que se ha cancelado el evento de entrada táctil.

El `TouchEffect` clase en la biblioteca estándar de .NET se deriva de `RoutingEffect` y define un evento denominado `TouchAction` y un método denominado `OnTouchAction` que invoca el `TouchAction` eventos:

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

Observe también el `Capture` propiedad. Para capturar los eventos de toque, una aplicación debe establecer esta propiedad en `true` anteriores a un `Pressed` eventos. En caso contrario, los eventos touch se comportan como los de la plataforma Universal de Windows.

La `TouchActionEventArgs` clase en la biblioteca estándar de .NET contiene toda la información que acompaña a cada evento:

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

Una aplicación puede utilizar el `Id` propiedad para realizar el seguimiento de los dedos individuales. Observe el `IsInContact` propiedad. Esta propiedad es siempre `true` para `Pressed` eventos y `false` para `Released` eventos. También es siempre `true` para `Moved` eventos en iOS y Android. El `IsInContact` propiedad podría ser `false` para `Moved` eventos en la plataforma Universal de Windows cuando se ejecuta el programa en el escritorio y el puntero del mouse se mueve sin un botón presionado.

Puede usar el `TouchEffect` clase en sus propias aplicaciones mediante la inclusión del archivo en el proyecto de biblioteca estándar de .NET de la solución y mediante la adición de una instancia de la `Effects` colección de cualquier elemento de Xamarin.Forms. Asociar un controlador para el `TouchAction` eventos para obtener los eventos touch.

Usar `TouchEffect` en su propia aplicación, también necesitará las implementaciones de plataforma que se incluye en **TouchTrackingEffectDemos** solución.

## <a name="the-touch-tracking-effect-implementations"></a>Las implementaciones de efecto de seguimiento de entrada táctil

IOS, Android y UWP las implementaciones de la `TouchEffect` se describen a continuación a partir de la implementación más sencilla (UWP) y terminando con la implementación de iOS porque es estructuralmente más compleja que las demás.

### <a name="the-uwp-implementation"></a>La implementación de UWP

La implementación de UWP de `TouchEffect` es la más sencilla. Como de costumbre, la clase se deriva de `PlatformEffect` e incluye dos atributos de ensamblado:

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

El `OnAttached` invalidación guarda cierta información como controladores de campos y los asocia a todos los eventos de puntero:

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

El `OnPointerPressed` controlador, invoca el evento efecto mediante una llamada a la `onTouchAction` campo el `CommonHandler` método:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` También comprueba el valor de la `Capture` propiedad de la clase efecto en la biblioteca estándar de .NET y las llamadas `CapturePointer` si es `true`.

 Los controladores de eventos UWP son incluso más sencillos:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>La implementación de Android

Las implementaciones de iOS y Android son necesariamente más complejas, porque debe implementar la `Exited` y `Entered` eventos cuando se mueve un dedo de un elemento a otro. Ambas implementaciones se estructuran de forma similar.

El Android `TouchEffect` clase instala un controlador para el `Touch` eventos:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

La clase también define dos diccionarios estáticos:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

El `viewDictionary` Obtiene una nueva entrada cada vez que el `OnAttached` se denomina invalidación:

```csharp
viewDictionary.Add(view, this);
```

Se quita la entrada del diccionario en `OnDetached`. Todas las instancias de `TouchEffect` está asociado a una vista concreta que está vinculado el efecto. El diccionario estático permite cualquier `TouchEffect` instancia para enumerar todas las vistas y sus correspondientes `TouchEffect` instancias. Esto es necesario para permitir la transferencia de los eventos de una vista a otra.

Android asigna un código de identificador en toque eventos que permite que una aplicación realizar el seguimiento de los dedos individuales. El `idToEffectDictionary` asocia este código de identificador con un `TouchEffect` instancia. Se agrega un elemento a este diccionario cuando la `Touch` una acción de presionar dedo se llama al controlador:

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

El elemento se quita de la `idToEffectDictionary` cuando se publica el dedo desde la pantalla. El `FireEvent` método simplemente acumula toda la información necesaria llamar a la `OnTouchAction` método:

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

Todos los demás tipos de entrada táctil se procesan de dos maneras diferentes: si la `Capture` propiedad es `true`, el evento de entrada táctil es una traducción bastante simple para el `TouchEffect` información. Obtiene más complicado cuando `Capture` es `false` como los eventos touch quizás tenga que mover de una vista a otra. Esto es responsabilidad de la `CheckForBoundaryHop` método, que se llama durante los eventos de movimiento. Este método hace uso de los diccionarios estáticos. Enumera a través de la `viewDictionary` para determinar la vista que usa actualmente el dedo, y utiliza `idToEffectDictionary` para almacenar actual `TouchEffect` instancia (y por lo tanto, la vista actual) asociado con un identificador determinado:

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

Si ha habido un cambio en el `idToEffectionDictionary`, el método potencialmente llama `FireEvent` para `Exited` y `Entered` para la transferencia de una vista a otra. Sin embargo, el dedo podría haberse movido a un área ocupado por una vista sin un adjunto `TouchEffect`, o desde dicha área en una vista con el efecto que se adjunta.

Observe el `try` y `catch` bloquear cuando se tiene acceso a la vista. En una página que se navega a la que, a continuación, vuelve a la página principal, el `OnDetached` no se llama el método y los elementos permanecen en el `viewDictionary` pero Android considera eliminado.

### <a name="the-ios-implementation"></a>La implementación de iOS

La implementación de iOS es similar a la implementación de Android salvo que los iOS `TouchEffect` clase debe crear una instancia de un derivado de `UIGestureRecognizer`. Se trata de una clase en el proyecto de iOS denominado `TouchRecognizer`. Esta clase mantiene dos diccionarios estáticos que almacenan `TouchRecognizer` instancias:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Gran parte de la estructura de este `TouchRecognizer` clase es similar a la Android `TouchEffect` clase.

## <a name="putting-the-touch-effect-to-work"></a>Poner el efecto de entrada táctil en funcionamiento

El [ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) programa contiene cinco páginas que probar el efecto de seguimiento de entrada táctil para realizar tareas comunes.

El **BoxView arrastrando** página le permite agregar `BoxView` elementos a un `AbsoluteLayout` y, a continuación, arrástrelos alrededor de la pantalla. El [archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml) crean instancias de dos `Button` vistas para agregar `BoxView` elementos a la `AbsoluteLayout` y borrar el `AbsoluteLayout`.

El método en el [archivo de código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs) que agrega un nuevo `BoxView` a la `AbsoluteLayout` también agrega un `TouchEffect` el objeto a la `BoxView` y asocia un controlador de eventos para el efecto:

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

El `TouchAction` procesa todos los eventos de toque el controlador de eventos para todas la `BoxView` elementos, pero debe tomar algunas precauciones: que no se permite que los dos dedos en un único servidor `BoxView` porque el programa sólo implementa arrastrando y los dos dedos podría interfieren entre sí. Por este motivo, la página define una clase incrustada para cada dedo está realizando el seguimiento:

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

El `dragDictionary` contiene una entrada para cada `BoxView` actualmente que se están arrastrando.

El `Pressed` táctil acción agrega un elemento a este diccionario y el `Released` acción quita. El `Pressed` lógica debe comprobar si ya hay un elemento en el diccionario para que `BoxView`. Si es así, el `BoxView` es ya que se están arrastrando y el nuevo evento es un segundo finger en ese mismo `BoxView`. Para el `Moved` y `Released` acciones, el controlador de eventos debe comprobar si el diccionario tiene una entrada para esa `BoxView` y que la entrada táctil `Id` propiedad para la que arrastró `BoxView` coincide con la de la entrada del diccionario:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

El `Pressed` conjuntos de lógica la `Capture` propiedad de la `TouchEffect` el objeto a `true`. Esto tiene el efecto de la entrega de todos los eventos subsiguientes para ese dedo al mismo controlador de eventos.

El `Moved` lógica mueve el `BoxView` modificando la `LayoutBounds` propiedad adjunta. El `Location` propiedad de los argumentos de evento siempre es relativa a la `BoxView` que se está arrastrando y si la `BoxView` se arrastra a una velocidad constante, el `Location` propiedades de los eventos consecutivos será aproximadamente el mismo. Por ejemplo, si presiona un dedo la `BoxView` en su centro, el `Pressed` acción almacenes un `PressPoint` propiedad de (50, 50), que sigue siendo el mismo para los eventos posteriores. Si el `BoxView` se arrastra diagonalmente a una velocidad constante, la subsiguiente `Location` propiedades durante la `Moved` acción podría ser valores de (55, 55), en cuyo caso el `Moved` lógica agrega 5 a la posición horizontal y vertical de la `BoxView`. Mueve el `BoxView` para que vuelve a su centro directamente bajo el dedo.

Puede mover varias `BoxView` elementos al mismo tiempo usando dedos diferentes.

[![](touch-tracking-images/boxviewdragging-small.png "Captura de pantalla triple de la página arrastrando BoxView")](touch-tracking-images/boxviewdragging-large.png#lightbox "Triple captura de pantalla de la página arrastrando BoxView")

### <a name="subclassing-the-view"></a>La vista de creación de subclases

A menudo, es más fácil para un elemento de Xamarin.Forms controlar sus propios eventos de entrada táctil. El **arrastrable BoxView arrastrando** página funciona igual que el **BoxView arrastrando** página, pero los elementos que el usuario arrastra es instancias de un [ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs) clase que deriva de `BoxView`:

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

El constructor crea y asocia el `TouchEffect`y establece el `Capture` propiedad cuando primero se crea una instancia de ese objeto. No hay ningún diccionario es necesario porque la propia clase almacena `isBeingDragged`, `pressPoint`, y `touchId` valores asociados con cada dedo. El `Moved` control modifica la `TranslationX` y `TranslationY` propiedades, por lo que funcionará la lógica incluso si el elemento primario de la `DraggableBoxView` no es un `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Integración con SkiaSharp

Las siguientes dos demostraciones requieren gráficos, y usan SkiaSharp para este propósito. Desea conocer [SkiaSharp uso de Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) antes de estudiar estos ejemplos. Los dos primeros artículos ("Conceptos básicos de dibujo de SkiaSharp" y "SkiaSharp líneas y las rutas de acceso") incluye todo lo que necesitará aquí.

El **elipse dibujo** página permite dibujar una elipse deslizando el dedo en la pantalla. Dependiendo de cómo mover el dedo, puede dibujar la elipse de la izquierda superior a la inferior derecha, o de cualquier otra esquina hasta la esquina opuesta. La elipse se dibuja con un color aleatorio y una opacidad.

[![](touch-tracking-images/ellipsedrawing-small.png "Captura de pantalla triple de la página de dibujo de elipse")](touch-tracking-images/ellipsedrawing-large.png#lightbox "Triple captura de pantalla de la página de dibujo de elipse")

Si, a continuación, pulsa en uno de los puntos suspensivos, puede arrastrarlo a otra ubicación. Esto requiere una técnica conocida como "prueba de posicionamiento," que implique la búsqueda para el objeto gráfico en un momento determinado. Los puntos suspensivos SkiaSharp no son elementos de Xamarin.Forms, por lo que no pueden realizar sus propias `TouchEffect` de procesamiento. El `TouchEffect` se debe aplicar a todo el `SKCanvasView` objeto.

El [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml) crea una instancia de archivo la `SKCanvasView` en una sola celda `Grid`. El `TouchEffect` objeto está asociado a la `Grid`:

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

En la plataforma Universal de Windows y Android el `TouchEffect` pueden conectar directamente a la `SKCanvasView`, pero en operaciones de E/s que no funcionan. Tenga en cuenta que la `Capture` propiedad está establecida en `true`.

Cada elipse que presenta SkiaSharp se representa mediante un objeto de tipo `EllipseDrawingFigure`:

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

El `StartPoint` y `EndPoint` propiedades se utilizan cuando el programa está procesando táctil de entrada; la `Rectangle` propiedad se utiliza para dibujar la elipse. El `LastFingerLocation` propiedad entra en juego cuando se arrastra la elipse y el `IsInEllipse` ayudas de método de prueba de posicionamiento. El método devuelve `true` si el punto está dentro de la elipse.

El [archivo de código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs) mantiene tres colecciones:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

El `draggingFigure` diccionario contiene un subconjunto de la `completedFigures` colección. El SkiaSharp `PaintSurface` controlador de eventos simplemente representa los objetos en estos la `completedFigures` y `inProgressFigures` colecciones:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

Es la parte más complicada del procesamiento táctil el `Pressed` control. Aquí es donde se realiza la prueba de posicionamiento, pero si el código detecta una elipse en el dedo del usuario, solo se puede arrastrar dicha elipse si actualmente no se está arrastrando por otro dedo. Si no hay ninguna elipse bajo el dedo del usuario, el código comienza el proceso de dibujar una elipse nueva:

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

Otro ejemplo SkiaSharp es el **pintura con los dedos** página. Puede seleccionar un color de trazo y el ancho del trazo de dos `Picker` vistas y, a continuación, se dibuja con los dedos uno o más:

[![](touch-tracking-images/fingerpaint-small.png "Captura de pantalla triple de la página de pintura con los dedos")](touch-tracking-images/fingerpaint-large.png#lightbox "Triple captura de pantalla de la página de pintura con los dedos")

Este ejemplo también necesita una clase independiente para representar cada línea dibujada en la pantalla:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

Un `SKPath` objeto se usa para representar cada línea. El [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs) archivo mantiene dos colecciones de estos objetos, uno para aquellas polilíneas que se está dibujando actualmente y otra para las polilíneas completadas:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

El `Pressed` crea un nuevo procesamiento `FingerPaintPolyline`, llamadas `MoveTo` en el objeto de ruta de acceso para almacenar el punto inicial y agrega ese objeto a la `inProgressPolylines` diccionario. El `Moved` procesamiento llamadas `LineTo` en el objeto de ruta de acceso con la nueva posición del dedo y el `Released` procesamiento transfiere la polilínea completada de `inProgressPolylines` a `completedPolylines`. Una vez más, el código de dibujo de SkiaSharp real es relativamente sencillo:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>Seguimiento para ver táctil

Todos los ejemplos anteriores se han establecido la `Capture` propiedad de la `TouchEffect` a `true`, cuando la `TouchEffect` se creó o cuando el `Pressed` se produjo el evento. Esto garantiza que el mismo elemento recibe todos los eventos asociados con el dedo que presionó en primer lugar la vista. El ejemplo final realiza *no* establecer `Capture` a `true`. Esto provoca un comportamiento diferente cuando se mueve un dedo en contacto con la pantalla de un elemento a otro. El elemento que se mueve el dedo desde recibe un evento con un `Type` propiedad establecida en `TouchActionType.Exited` y el segundo elemento recibe un evento con un `Type` de `TouchActionType.Entered`.

Este tipo de procesamiento de entrada táctil es muy útil para un teclado de música. Una clave debe ser capaz de detectar cuando se presiona, sino también cuando un dedo diapositivas de una clave a otro.

El **teclado silencioso** página define pequeño [ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs) y [ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs) clases que derivan de [ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs), que deriva de `BoxView`.

La `Key` clase está lista para usarse en un programa de música real. Define propiedades públicas denominadas `IsPressed` y `KeyNumber`, que está pensada para establecerse en el código de tecla establecido por el estándar MIDI. El `Key` clase también define un evento denominado `StatusChanged`, que se invoca cuando el `IsPressed` cambios de propiedad.

Se permiten varios dedos en cada clave. Por este motivo, el `Key` clase mantiene una `List` de los números de Id. de entrada táctil de todos los dedos actualmente tocar esa clave:

```csharp
List<long> ids = new List<long>();
```

El `TouchAction` controlador de eventos agrega un identificador para el `ids` lista para ambos un `Pressed` tipo de evento y un `Entered` tipo, pero solo cuando la `IsInContact` propiedad es `true` para el `Entered` eventos. El identificador se quita de la `List` para un `Released` o `Exited` eventos:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

El `AddToList` y `RemoveFromList` ambos métodos, comprueban si el `List` ha cambiado entre vacío y no está vacío y si es así, se invoca el `StatusChanged` eventos.

Los distintos `WhiteKey` y `BlackKey` elementos se organizan en la página [archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml), lo que mejor se ve cuando el teléfono se mantiene en un modo horizontal:

[![](touch-tracking-images/silentkeyboard-small.png "Captura de pantalla triple de la página de teclado silencioso")](touch-tracking-images/silentkeyboard-large.png#lightbox "Triple captura de pantalla de la página de teclado silenciosa")

Si barrer el dedo a través de las claves, verá la ligera cambios en el color que se transfieren los eventos touch de una clave a otro.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo invocar los eventos de un efecto y cómo escribir y usar un efecto que implementa el procesamiento multitoque bajo nivel.


## <a name="related-links"></a>Vínculos relacionados

- [Seguimiento de multitoque dedo en iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Dedo multitoque seguimiento en Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Efecto de seguimiento de entrada táctil (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
