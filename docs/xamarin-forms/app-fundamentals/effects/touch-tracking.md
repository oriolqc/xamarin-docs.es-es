---
title: Invocación de eventos de efectos
description: Un efecto puede definir e invocar un evento, los cambios en la vista nativo subyacente de señalización. En este artículo se muestra cómo implementar el dedo de la tecnología multitoque bajo nivel de seguimiento y cómo se generan eventos que indican actividad táctil.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 0a5e2c1a7a7807da91fd98e617467ea251a25bc0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527409"
---
# <a name="invoking-events-from-effects"></a>Invocación de eventos de efectos

_Un efecto puede definir e invocar un evento, los cambios en la vista nativo subyacente de señalización. En este artículo se muestra cómo implementar el dedo de la tecnología multitoque bajo nivel de seguimiento y cómo se generan eventos que indican actividad táctil._

El efecto que se describe en este artículo proporciona acceso a eventos de función táctil de bajo nivel. Estos eventos de bajo nivel no están disponibles a través de las existentes `GestureRecognizer` clases, pero son vitales para algunos tipos de aplicaciones. Por ejemplo, un finger-paint aplicación necesita realizar un seguimiento de los dedos individuales cuando se mueven en la pantalla. Un teclado de música debe detectar derivaciones y versiones en las claves individuales, así como un dedo deslizante de una clave a otra en un glissando.

Un efecto es ideal para el seguimiento de multitoque dedo, ya que puede asociarse a cualquier elemento de Xamarin.Forms.

## <a name="platform-touch-events"></a>Eventos de toque de plataforma

IOS, Android y plataforma Universal de Windows incluyen una API de bajo nivel que permite a las aplicaciones detectar tocar la actividad. Estas plataformas de que todos distinguen entre los tres tipos básicos táctil eventos:

- *Presiona*, cuando un dedo toca la pantalla
- *Mover*, cuando se mueve un dedo toca la pantalla
- *Publicado*, cuando se suelta el dedo desde la pantalla

En un entorno multitoque, varios dedos pueden tocar la pantalla al mismo tiempo. Las diferentes plataformas incluyen un número de identificación (ID) que las aplicaciones pueden usar para distinguir entre varios dedos.

En iOS, el `UIView` clase define tres métodos reemplazables, `TouchesBegan`, `TouchesMoved`, y `TouchesEnded` correspondientes a estos tres eventos básicos. El artículo [multitoque dedo seguimiento](~/ios/app-fundamentals/touch/touch-tracking.md) se describe cómo usar estos métodos. Sin embargo, un programa de iOS no necesita invalidar una clase que derive de `UIView` usar estos métodos. IOS `UIGestureRecognizer` también define estos mismos tres métodos y se puede vincular una instancia de una clase que deriva de `UIGestureRecognizer` a cualquier `UIView` objeto.

En Android, el `View` clase define un método reemplazable denominado `OnTouchEvent` para procesar toda la actividad de toque. El tipo de la actividad táctil se define por miembros de la enumeración `Down`, `PointerDown`, `Move`, `Up`, y `PointerUp` tal como se describe en el artículo [multitoque dedo seguimiento](~/android/app-fundamentals/touch/touch-tracking.md). Android `View` también define un evento denominado `Touch` que permite que un controlador de eventos que se adjuntará a cualquier `View` objeto.

En la plataforma Universal de Windows (UWP), el `UIElement` clase define eventos denominados `PointerPressed`, `PointerMoved`, y `PointerReleased`. Estos métodos se describen en el artículo [artículo controlar la entrada de puntero de MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) y la documentación de API para el [ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/) clase.

El `Pointer` API en la plataforma Universal de Windows está diseñado para unificar el mouse, el tacto y entrada manuscrita. Por ese motivo, la `PointerMoved` evento se invoca cuando el mouse se mueve a través de un elemento, incluso cuando no se presionó un botón del mouse. El `PointerRoutedEventArgs` objeto que acompaña a estos eventos tiene una propiedad denominada `Pointer` que tiene una propiedad denominada `IsInContact` que indica si se presiona un botón del mouse o un dedo está en contacto con la pantalla.

Además, la UWP define dos eventos más denominados `PointerEntered` y `PointerExited`. Estos errores indican cuando un dedo o el mouse se mueve de un elemento a otro. Por ejemplo, considere la posibilidad de dos elementos adyacentes con el nombre de A y B. Ambos elementos han instalado a controladores para los eventos de puntero. Cuando se presiona un dedo en A, el `PointerPressed` se invoca el evento. Cuando se mueve el dedo, invoca un `PointerMoved` eventos. Si mueve el dedo de la A B, se invoca A un `PointerExited` eventos y B invoca un `PointerEntered` eventos. Si, a continuación, se libera el dedo, B invoca un `PointerReleased` eventos.

Las plataformas iOS y Android son diferentes de UWP: la vista que primero se obtiene la llamada a `TouchesBegan` o `OnTouchEvent` cuando un dedo toca la vista continúa obtener toda la actividad de interacción, incluso si se mueve el dedo a distintas vistas. UWP puede comportarse de forma similar si la aplicación captura el puntero: en el `PointerEntered` controlador de eventos, las llamadas de elemento `CapturePointer` y, a continuación, obtiene toda la actividad táctil de ese dedo.

El enfoque UWP demuestra para ser muy útil para algunos tipos de aplicaciones, por ejemplo, un teclado de música. Cada clave puede controlar los eventos de toque para esa clave y detectar cuándo un dedo se deslizan desde una clave a otra mediante el `PointerEntered` y `PointerExited` eventos.

Por ese motivo, el efecto de seguimiento de toque se describe en este artículo implementa el enfoque UWP.

## <a name="the-touch-tracking-effect-api"></a>La API de seguimiento de la interacción de efecto

El [ **Touch demostraciones de efecto de seguimiento** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) ejemplo contiene las clases (y una enumeración) que implementan el seguimiento de táctil de bajo nivel. Estos tipos pertenecen al espacio de nombres `TouchTracking` y comenzar con la palabra `Touch`. El **TouchTrackingEffectDemos** proyecto de biblioteca de .NET Standard incluye la `TouchActionType` enumeración para el tipo de eventos táctiles:

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

Todas las plataformas incluyen también un evento que indica que se ha cancelado el evento de toque.

El `TouchEffect` deriva de la clase de la biblioteca .NET Standard `RoutingEffect` y define un evento denominado `TouchAction` y un método denominado `OnTouchAction` que invoca la `TouchAction` eventos:

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

Observe también la `Capture` propiedad. Para capturar eventos de toque, una aplicación debe establecer esta propiedad en `true` anteriores a un `Pressed` eventos. En caso contrario, los eventos de toque se comportan como las de la plataforma Universal de Windows.

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

Una aplicación puede utilizar el `Id` propiedad para el seguimiento de los dedos individuales. Tenga en cuenta el `IsInContact` propiedad. Esta propiedad es siempre `true` para `Pressed` eventos y `false` para `Released` eventos. También es siempre `true` para `Moved` eventos en iOS y Android. El `IsInContact` podría ser propiedad `false` para `Moved` eventos en la plataforma Universal de Windows cuando se ejecuta el programa en el escritorio y se mueve el puntero del mouse sin un botón presionado.

Puede usar el `TouchEffect` clase en sus propias aplicaciones mediante la inclusión del archivo de proyecto de biblioteca estándar de .NET de la solución y mediante la adición de una instancia de la `Effects` colección de cualquier elemento de Xamarin.Forms. Adjuntar un controlador para el `TouchAction` eventos para obtener los eventos de toque.

Para usar `TouchEffect` en su propia aplicación, también tendrá que incluye en las implementaciones de plataforma **TouchTrackingEffectDemos** solución.

## <a name="the-touch-tracking-effect-implementations"></a>Las implementaciones de efecto de seguimiento de toque

IOS, Android y UWP implementaciones de la `TouchEffect` se describen a continuación comienza con la implementación más sencilla (UWP) y termina con la implementación de iOS porque es estructuralmente más compleja que los demás.

### <a name="the-uwp-implementation"></a>La implementación de UWP

La implementación de UWP de `TouchEffect` es el más sencillo. Como de costumbre, la clase se deriva de `PlatformEffect` e incluye dos atributos de ensamblado:

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

El `OnAttached` invalidación guarda información como los controladores de los campos y los asocia a todos los eventos de puntero:

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

El `OnPointerPressed` controlador invoca el evento de efecto mediante una llamada a la `onTouchAction` campo el `CommonHandler` método:

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

`OnPointerPressed` También comprueba el valor de la `Capture` propiedad de la clase efecto en la biblioteca de .NET Standard y llama a `CapturePointer` si es `true`.

 Los otros controladores de eventos UWP son incluso más sencillos:

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

Las implementaciones de Android e iOS son necesariamente más complejas porque deben implementar la `Exited` y `Entered` eventos cuando un dedo se mueve de un elemento a otro. Ambas implementaciones tienen una estructura similar.

Android `TouchEffect` clase instala un controlador para el `Touch` eventos:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

La clase define también dos diccionarios estáticos:

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

El `viewDictionary` Obtiene una nueva entrada cada vez el `OnAttached` se llama a la invalidación:

```csharp
viewDictionary.Add(view, this);
```

Se quita la entrada del diccionario en `OnDetached`. Todas las instancias de `TouchEffect` está asociado a una vista concreta que está conectado el efecto. El diccionario estático permite cualquier `TouchEffect` instancia para enumerar todas las vistas y sus correspondientes `TouchEffect` instancias. Esto es necesario para permitir la transferencia de los eventos de una vista a otra.

Android asigna un código de identificador que tocar los eventos que permite que una aplicación realizar un seguimiento de los dedos individuales. El `idToEffectDictionary` asocia este código de identificador con un `TouchEffect` instancia. Se agrega un elemento a este diccionario cuando el `Touch` se llama al controlador para presionar un dedo:

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

El elemento se quitó el `idToEffectDictionary` cuando se lance el dedo desde la pantalla. El `FireEvent` método simplemente acumula toda la información necesaria llamar a la `OnTouchAction` método:

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

Se procesan todos los demás tipos de interacción de dos maneras diferentes: si el `Capture` propiedad es `true`, el evento de toque es una traducción bastante simple para el `TouchEffect` información. Lo más complicada cuando `Capture` es `false` como los eventos de toque quizás tenga que moverse de una vista a otro. Esta es la responsabilidad de la `CheckForBoundaryHop` método, que se llama durante los eventos de movimiento. Este método usa dos diccionarios estáticos. Enumera a través de la `viewDictionary` para determinar la vista que actualmente esté tocando el dedo y usa `idToEffectDictionary` almacenar actual `TouchEffect` instancia (y por lo tanto, la vista actual) asociado con un identificador determinado:

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

Si ha habido un cambio en el `idToEffectDictionary`, el método llama a potencialmente `FireEvent` para `Exited` y `Entered` para transferencia de una vista a otra. Sin embargo, el dedo es posible que se han movido a un área ocupado por una vista sin un adjunto `TouchEffect`, o desde esa área a una vista con el efecto que se adjunta.

Tenga en cuenta la `try` y `catch` bloquear cuando se tiene acceso a la vista. En una página que se navega a la que, a continuación, navega a la página principal, el `OnDetached` no se llama al método y los elementos permanecen en el `viewDictionary` pero Android considera eliminado.

### <a name="the-ios-implementation"></a>La implementación de iOS

La implementación de iOS es similar a la implementación de Android, salvo que iOS `TouchEffect` clase debe crear una instancia de un derivado de `UIGestureRecognizer`. Se trata de una clase en el proyecto de iOS denominado `TouchRecognizer`. Esta clase mantiene dos diccionarios estáticos que almacenan `TouchRecognizer` instancias:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Gran parte de la estructura de este `TouchRecognizer` clase es similar a la de Android `TouchEffect` clase.

## <a name="putting-the-touch-effect-to-work"></a>Poner el efecto de toque en funcionamiento

El [ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) programa contiene cinco páginas que probar el efecto de seguimiento de interacción para tareas comunes.

El **BoxView arrastrar** página le permite agregar `BoxView` elementos a un `AbsoluteLayout` y, a continuación, arrástrelos en torno a la pantalla. El [archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml) crean instancias de dos `Button` vistas para agregar `BoxView` elementos a la `AbsoluteLayout` y borrar el `AbsoluteLayout`.

El método en el [archivo de código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs) que agrega un nuevo `BoxView` a la `AbsoluteLayout` también agrega un `TouchEffect` de objeto para el `BoxView` y adjunta un controlador de eventos para el efecto:

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

El `TouchAction` procesa todos los eventos de toque el controlador de eventos para todas la `BoxView` elementos, pero necesita algunos cuidado: no puede permitir que dos dedos en una sola `BoxView` porque el programa sólo implementa arrastrando y los dos dedos podría interfieren entre sí. Por este motivo, la página define una clase incrustada para cada dedo que está realizando el seguimiento:

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

El `dragDictionary` contiene una entrada para cada `BoxView` que se arrastran actualmente.

El `Pressed` acción táctil agrega un elemento a este diccionario y el `Released` acción lo quita. El `Pressed` lógica debe comprobar si ya hay un elemento en el diccionario para que `BoxView`. Si es así, el `BoxView` es el nuevo evento y que se arrastran ya es un segundo dedo en ese mismo `BoxView`. Para el `Moved` y `Released` acciones, el controlador de eventos debe comprobar si el diccionario tiene una entrada para que `BoxView` y que la entrada táctil `Id` propiedad para la que arrastró `BoxView` coincide con el que aparece en la entrada del diccionario:

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

El `Pressed` conjuntos de lógica el `Capture` propiedad de la `TouchEffect` objeto `true`. Esto tiene el efecto de la entrega de todos los eventos subsiguientes para ese dedo en el mismo controlador de eventos.

El `Moved` lógica mueve el `BoxView` modificando el `LayoutBounds` propiedad adjunta. El `Location` propiedad de los argumentos de evento siempre es relativa a la `BoxView` que se está arrastrando y si el `BoxView` se está arrastrando a una velocidad constante, el `Location` propiedades de los eventos consecutivos será aproximadamente el mismo. Por ejemplo, si presiona un dedo el `BoxView` en su centro, el `Pressed` acción almacenes un `PressPoint` propiedad de (50, 50), que sigue siendo el mismo para los eventos posteriores. Si el `BoxView` se arrastra en diagonal a una velocidad constante, las posterior `Location` propiedades durante el `Moved` acción podría ser valores de (55, 55), en cuyo caso el `Moved` 5 agrega lógica a la posición horizontal y vertical de la `BoxView`. Mueve el `BoxView` por lo que vuelve a su centro directamente bajo el dedo.

Puede mover varios `BoxView` elementos al mismo tiempo usando dedos diferentes.

[![](touch-tracking-images/boxviewdragging-small.png "Captura de pantalla de la página BoxView arrastrar triple")](touch-tracking-images/boxviewdragging-large.png#lightbox "Triple captura de pantalla de la página BoxView arrastrar")

### <a name="subclassing-the-view"></a>Creación de subclases de la vista

A menudo, resulta más fácil para un elemento de Xamarin.Forms controlar sus propios eventos de toque. El **arrastrable BoxView arrastrar** página funciona igual que el **BoxView arrastrar** página, pero los elementos que el usuario arrastra es instancias de un [ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs) clase que derive de `BoxView`:

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

El constructor crea y adjunta el `TouchEffect`y establece el `Capture` propiedad cuando primero se crea una instancia de ese objeto. No hay ningún diccionario es necesario porque la propia clase almacena `isBeingDragged`, `pressPoint`, y `touchId` valores asociados con cada dedo. El `Moved` control modifica el `TranslationX` y `TranslationY` propiedades, por lo que funcionará la lógica de incluso si el elemento primario de la `DraggableBoxView` no es un `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Integración de SkiaSharp

Las siguientes dos demostraciones requieren gráficos, y usan SkiaSharp para este propósito. Desea obtener información sobre [utilizando SkiaSharp en Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) antes de estudiar estos ejemplos. Los dos primeros artículos ("Conceptos básicos de dibujo de SkiaSharp" y "SkiaSharp líneas y las rutas de acceso") incluye todo lo que necesitará aquí.

El **elipse dibujo** página le permite dibujar una elipse deslizando el dedo en la pantalla. Dependiendo de cómo mover el dedo, puede dibujar la elipse desde la izquierda superior a la esquina inferior derecha, o desde cualquier otra esquina hasta la esquina opuesta. La elipse se dibuja con un color aleatorio y una opacidad.

[![](touch-tracking-images/ellipsedrawing-small.png "Captura de pantalla de la página de dibujo de elipse triple")](touch-tracking-images/ellipsedrawing-large.png#lightbox "Triple captura de pantalla de la página de dibujo de puntos suspensivos")

Si, a continuación, toque uno de los puntos suspensivos, puede arrastrarlo a otra ubicación. Esto requiere una técnica conocida como "prueba de posicionamiento," lo que implica buscar el objeto gráfico en un momento determinado. Los puntos suspensivos de SkiaSharp no son elementos de Xamarin.Forms, por lo que no se pueden realizar sus propias `TouchEffect` de procesamiento. El `TouchEffect` debe aplicar a todo el `SKCanvasView` objeto.

El [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml) crea una instancia de archivo del `SKCanvasView` en una sola celda `Grid`. El `TouchEffect` objeto se asocia a la que `Grid`:

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

En la plataforma Universal de Windows y Android el `TouchEffect` se pueden conectar directamente a la `SKCanvasView`, pero en iOS que no funciona. Tenga en cuenta que el `Capture` propiedad está establecida en `true`.

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

El `StartPoint` y `EndPoint` propiedades se utilizan cuando el programa está procesando táctil de entrada; el `Rectangle` propiedad se utiliza para dibujar la elipse. El `LastFingerLocation` propiedad entra en juego cuando se arrastra la elipse y el `IsInEllipse` ayudas de método de prueba de posicionamiento. El método devuelve `true` si el punto está dentro de la elipse.

El [archivo de código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs) mantiene tres colecciones:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

El `draggingFigure` diccionario contiene un subconjunto de los `completedFigures` colección. El SkiaSharp `PaintSurface` controlador de eventos representa simplemente los objetos en estos el `completedFigures` y `inProgressFigures` colecciones:

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

Es la parte más complicada del procesamiento toque el `Pressed` de control. Aquí es donde se realiza la prueba de posicionamiento, pero si el código detecta una elipse con los dedos del usuario, solo se puede arrastrar ese elipse si actualmente no se está arrastrando por otro dedo. Si no hay ninguna elipse con los dedos del usuario, el código comienza el proceso de dibujar una elipse nueva:

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

Otro ejemplo de SkiaSharp es el **pintura con los dedos** página. Puede seleccionar un color de trazo y el ancho del trazo de dos `Picker` vistas y, a continuación, dibujar con uno o más dedos:

[![](touch-tracking-images/fingerpaint-small.png "Captura de pantalla de la página de pintura con los dedos triple")](touch-tracking-images/fingerpaint-large.png#lightbox "Triple captura de pantalla de la página de pintura con los dedos")

Este ejemplo también requiere una clase independiente para representar cada línea dibujada en la pantalla:

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

El `Pressed` crea un nuevo procesamiento `FingerPaintPolyline`, llamadas `MoveTo` en el objeto de ruta de acceso para almacenar el punto inicial y agrega ese objeto a la `inProgressPolylines` diccionario. El `Moved` procesar llamadas `LineTo` en el objeto de trazado con la nueva posición del dedo y el `Released` procesamiento transfiere la polilínea completada desde `inProgressPolylines` a `completedPolylines`. Una vez más, el código de dibujo de SkiaSharp real es relativamente sencillo:

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

### <a name="tracking-view-to-view-touch"></a>Para ver táctil de seguimiento

Todos los ejemplos anteriores se han establecido la `Capture` propiedad de la `TouchEffect` a `true`, cuando el `TouchEffect` se creó o cuando el `Pressed` se produjo el evento. Esto garantiza que el mismo elemento recibe todos los eventos asociados con el dedo que primero se presionó la vista. Hace el ejemplo final *no* establecer `Capture` a `true`. Esto provoca un comportamiento diferente cuando se mueve un dedo en contacto con la pantalla de un elemento a otro. El elemento que se mueve el dedo desde recibe un evento con un `Type` propiedad establecida en `TouchActionType.Exited` y el segundo elemento recibe un evento con un `Type` de `TouchActionType.Entered`.

Este tipo de procesamiento de toque es muy útil para un teclado de música. Una clave debe ser capaz de detectar cuándo se presiona, sino también cuando un dedo diapositivas de una clave a otra.

El **teclado silenciosa** página define pequeño [ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs) y [ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs) clases que derivan de [ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs), que se deriva de `BoxView`.

La `Key` clase está lista para usarse en un programa real de música. Define propiedades públicas denominadas `IsPressed` y `KeyNumber`, que está pensada para establecerse en el código clave que se establece mediante el estándar MIDI. El `Key` clase también define un evento denominado `StatusChanged`, que se invoca cuando el `IsPressed` los cambios de propiedad.

Se permiten varios dedos en cada clave. Por este motivo, el `Key` clase mantiene una `List` de los números de Id. de interacción de todos los dedos que toquen actualmente esa clave:

```csharp
List<long> ids = new List<long>();
```

El `TouchAction` controlador de eventos agrega un identificador para el `ids` lista para ambos un `Pressed` tipo de evento y un `Entered` tipo, pero solo cuando el `IsInContact` propiedad es `true` para el `Entered` eventos. Se quita el identificador de la `List` para un `Released` o `Exited` eventos:

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

El `AddToList` y `RemoveFromList` dos métodos que se comprueban si el `List` ha cambiado entre vacío y no vacío y si es así, invoca el `StatusChanged` eventos.

Los distintos `WhiteKey` y `BlackKey` se organizan los elementos en la página [archivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml), que tiene mejor aspecto cuando se mantiene el teléfono en un modo horizontal:

[![](touch-tracking-images/silentkeyboard-small.png "Captura de pantalla triple de la página teclado silenciosa")](touch-tracking-images/silentkeyboard-large.png#lightbox "Triple captura de pantalla de la página teclado silenciosa")

Si barrido el dedo a través de las claves, podrá ver por los pequeños cambios en el color que se transfieren los eventos de toque de una clave a otra.

## <a name="summary"></a>Resumen

Este artículo demuestra cómo invocar eventos de un efecto y cómo escribir y usar un efecto que implementa el procesamiento de multitoque de bajo nivel.


## <a name="related-links"></a>Vínculos relacionados

- [Seguimiento de la tecnología multitoque dedo en iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Dedo multitoque de seguimiento en Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Toque el efecto de seguimiento (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
