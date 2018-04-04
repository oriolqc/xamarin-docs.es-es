---
title: 'Tutorial: Using táctil en iOS'
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 58066ef0071c8105658f0d766e8f038b2bd3ddf2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough--using-touch-in-ios"></a>Tutorial: Using táctil en iOS

Este tutorial muestra cómo escribir código que responda a diferentes tipos de eventos de toque. Cada ejemplo se encuentra en una pantalla independiente:

- [Touch ejemplos](#Touch_Samples) : cómo responder a la entrada táctil eventos.
- [Ejemplos de reconocimiento de gestos](#Gesture_Recognizer_Samples) : cómo usar identificadores de gestos integrados.
- [Ejemplo de gestos reconocedor personalizado](#Custom_Gesture_Recognizer) : cómo crear un reconocedor de movimiento personalizada.

Cada sección contiene instrucciones para escribir el código desde el principio.
El [a partir de código de ejemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) ya incluye una pantalla completa de guión gráfico y menú:

 [![](ios-touch-walkthrough-images/image3.png "El ejemplo incluye pantalla del menú")](ios-touch-walkthrough-images/image3.png#lightbox)

Siga las instrucciones siguientes para agregar código al guión gráfico y obtener información sobre los distintos tipos de eventos de toque disponibles en iOS. Como alternativa, abra el [final de ejemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final) para ver todo en funcionamiento.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Ejemplos de entrada táctil

En este ejemplo, se muestran algunas de las API táctiles. Siga estos pasos para agregar el código necesario para implementar los eventos de entrada táctil:


1. Abra el proyecto **Touch_Start**. Primero ejecute el proyecto para asegurarse de que todo es correcto y forma táctil el **ejemplos táctil** botón. Debería ver una pantalla similar al siguiente (aunque ninguno de los botones funcionará):
    
    [![](ios-touch-walkthrough-images/image4.png "Aplicación de ejemplo que se ejecute con botones no laboral")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Edite el archivo **TouchViewController.cs** y agregue las siguientes variables de instancia de dos a la clase `TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implemente el `TouchesBegan` método, tal como se muestra en el código siguiente:

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    Este método funciona buscando una `UITouch` del objeto y, si existe, realizar alguna acción en función de donde se produjo la entrada táctil:

    * _Dentro de TouchImage_ : muestra el texto `Touches Began` en una etiqueta y cambiar la imagen.
    * _Dentro de DoubleTouchImage_ : cambiar la imagen que se muestra si el movimiento es un doble punteo.
    * _Dentro de DragImage_ : establecer una marca que indica que se ha iniciado la entrada táctil. El método `TouchesMoved` utilizará esta marca para determinar si `DragImage` debe desplazar por la pantalla o no, como veremos en el paso siguiente.

    El código anterior solo se ocupa de los últimos retoques individuales, no hay todavía ningún comportamiento si el usuario está moviendo el dedo en la pantalla. Para responder a la extracción, implemente `TouchesMoved` tal como se muestra en el código siguiente:

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    Este método obtiene un `UITouch` objeto y, a continuación, comprueba que se produjo la entrada táctil. Si se ha producido la entrada táctil en `TouchImage`, a continuación, el texto que se mueven los últimos retoques se muestra en la pantalla. 

    Si `touchStartedInside` es true, sabemos que el usuario tiene su dedo `DragImage` y se moverlo. El código se moverá `DragImage` cuando el usuario mueve el dedo por la pantalla.

1. Es necesario controlar el caso cuando el usuario levanta el dedo incumple su fuera de la pantalla o iOS cancela el evento de entrada táctil. Para ello, implementaremos `TouchesEnded` y `TouchesCancelled` tal y como se muestra a continuación:

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```
    
    Ambos métodos se restablecerán la `touchStartedInside` marca en false. `TouchesEnded` También debe mostrar `TouchesEnded` en la pantalla.

1. En este punto se finaliza la pantalla táctil ejemplos. Observe cómo la pantalla cambia al interactuar con cada una de las imágenes, como se muestra en la captura de pantalla siguiente:
        
    [![](ios-touch-walkthrough-images/image4.png "La pantalla inicial de la aplicación")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "La pantalla después de que el usuario arrastra un botón")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

##  <a name="gesture-recognizer-samples"></a>Ejemplos de reconocimiento de gestos

El [sección anterior](#Touch_Samples) se ha mostrado cómo arrastrar un objeto alrededor de la pantalla mediante el uso de eventos touch.
En esta sección se se eliminará de los eventos de toque y muestran cómo utilizar los siguientes identificadores de gestos:

-  El `UIPanGestureRecognizer` para arrastrar una imagen alrededor de la pantalla.
-  El `UITapGestureRecognizer` para responder a las derivaciones de tipo double en la pantalla.

Si ejecuta el [a partir de código de ejemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) y haga clic en el **gesto reconocedor ejemplos** botón, debería ver la siguiente pantalla:

 [![](ios-touch-walkthrough-images/image6.png "Al hacer clic en el botón de gestos reconocedor ejemplos muestran esta pantalla")](ios-touch-walkthrough-images/image6.png#lightbox)

Siga estos pasos para implementar los identificadores de gestos:


1. Edite el archivo **GestureViewController.cs** y agregue la siguiente variable de instancia:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Necesitamos esta variable de instancia para realizar un seguimiento de la ubicación anterior de la imagen.
Va a usar el reconocedor de movimientos de panorámica el `originalImageFrame` valor para calcular el ajuste necesario para volver a dibujar la imagen en la pantalla.

1. Agregue el método siguiente al controlador:

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    Este código crea un `UIPanGestureRecognizer` de instancia y lo agrega a una vista.
Tenga en cuenta que asignamos un destino para el movimiento en el formulario del método `HandleDrag` : este método se proporciona en el paso siguiente.

1. Para implementar HandleDrag, agregue el código siguiente al controlador:

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    El código anterior se compruebe primero el estado del reconocedor de movimientos y, a continuación, mover la imagen alrededor de la pantalla. Con este código en su lugar, el controlador ahora puede admitir arrastrando el una imagen alrededor de la pantalla.


1. Agregar un `UITapGestureRecognizer` Esto cambiará la imagen que se muestra en DoubleTouchImage. Agregue el método siguiente a la `GestureViewController` controlador:

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    Este código es muy similar al código de la `UIPanGestureRecognizer` , pero en lugar de utilizar un delegado para un destino que estamos usando un `Action`. 

1. Lo último que debemos hacer es modificar `ViewDidLoad` para que llama a los métodos que acabamos de agregar. Cambiar ViewDidLoad por lo que es similar al código siguiente:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    Observe también que se inicialice el valor de `originalImageFrame`.


1. Ejecute la aplicación e interactuar con las dos imágenes.
Captura de pantalla siguiente es un ejemplo de estas interacciones:
    
    [![](ios-touch-walkthrough-images/image7.png "Esta captura de pantalla muestra una interacción de arrastrar")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Reconocedor de gestos personalizados

En esta sección se aplicará los conceptos de las secciones anteriores para crear un reconocedor de movimiento personalizada. El reconocedor de movimientos personalizada le subclases `UIGestureRecognizer`y se reconoce cuando el usuario dibuja una "V" en la pantalla, a continuación, activar o desactivar un mapa de bits. Captura de pantalla siguiente es un ejemplo de esta pantalla:

 [![](ios-touch-walkthrough-images/image8.png "Reconocerá la aplicación cuando el usuario dibuja una "V" en la pantalla")](ios-touch-walkthrough-images/image8.png#lightbox)

Siga estos pasos para crear un reconocedor de movimiento personalizado:


1. Agregue una nueva clase al proyecto denominado `CheckmarkGestureRecognizer`y darle un aspecto similar al código siguiente:

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    Se llama al método Reset cuando la `State` propiedad cambia a `Recognized` o `Ended`. Este es el momento de restablecer cualquier estado interno establecido en el reconocedor de movimientos personalizado.
Ahora la clase puede empezar desde cero próxima vez que el usuario interactúa con la aplicación y esté listo para volver a intentar reconocer el movimiento.



1. Ahora que hemos definido un reconocedor de movimiento personalizada (`CheckmarkGestureRecognizer`) editar el **CustomGestureViewController.cs** de archivos y agregue las siguientes variables de instancia de dos:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Para crear una instancia y configurar el reconocedor de movimiento, agregue el método siguiente al controlador:

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. Editar `ViewDidLoad` para que llama `WireUpCheckmarkGestureRecognizer`, tal y como se muestra en el siguiente fragmento de código:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Ejecute la aplicación y pruebe a dibujar una "V" en la pantalla. Debería ver la imagen que se va a mostrar el cambio, como se muestra en las capturas de pantalla siguiente:
    
    [![](ios-touch-walkthrough-images/image9.png "El botón Comprobar")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "El botón no está activado")](ios-touch-walkthrough-images/image10.png#lightbox)



Los pasos anteriores en tres secciones demostrado maneras diferentes para responder a la entrada táctil eventos en iOS: con eventos de toque, identificadores de gestos integrados, o con un reconocedor de movimiento personalizada.



## <a name="related-links"></a>Vínculos relacionados

- [iOS Touch iniciar (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
