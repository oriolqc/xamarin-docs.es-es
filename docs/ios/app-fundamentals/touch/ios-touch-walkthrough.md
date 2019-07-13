---
title: 'Tutorial: Uso de interacción en Xamarin.iOS'
description: Este documento describe cómo controlar la entrada táctil en aplicaciones de Xamarin.iOS, tratar las interacciones táctiles de ejemplo, los reconocedores de gestos y los reconocedores de gestos personalizados.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: abcd89a6c4547680df0b96d235531312d3b21c52
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864334"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Tutorial: Uso de interacción en Xamarin.iOS

Este tutorial muestra cómo escribir código que responde a los distintos tipos de eventos de toque. Cada ejemplo se encuentra en otra pantalla:

- [Ejemplos de interacción](#Touch_Samples) : cómo responder a eventos de toque.
- [Ejemplos del reconocedor de gestos](#Gesture_Recognizer_Samples) : uso de los reconocedores de gestos integrados.
- [Ejemplo reconocedor de gestos personalizados](#Custom_Gesture_Recognizer) : cómo compilar un reconocedor de movimiento personalizada.

Cada sección contiene instrucciones para escribir el código desde cero.
El [a partir de código de ejemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) ya incluye una pantalla completa de guión gráfico y menú:

 [![](ios-touch-walkthrough-images/image3.png "El ejemplo incluye la pantalla del menú")](ios-touch-walkthrough-images/image3.png#lightbox)

Siga las instrucciones siguientes para agregar código al guión gráfico y obtenga información sobre los diferentes tipos de eventos de toque disponibles en iOS. Como alternativa, abra el [ejemplo terminado](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final) ver todo lo que trabajar.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Ejemplos de toque

En este ejemplo, se muestran algunas de las API táctiles. Siga estos pasos para agregar el código necesario para implementar eventos de función táctil:


1. Abra el proyecto **Touch_Start**. Primero ejecute el proyecto para asegurarse de que todo está correcto y toque el **Touch ejemplos** botón. Debería ver una pantalla similar a la siguiente (aunque ninguno de los botones funcionará):
    
    [![](ios-touch-walkthrough-images/image4.png "Aplicación de ejemplo que se ejecute con los botones no funciona.")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Edite el archivo **TouchViewController.cs** y agregue las siguientes variables de instancia de dos a la clase `TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implemente el `TouchesBegan` método, como se muestra en el código siguiente:

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
    
    Este método funciona mediante la comprobación para un `UITouch` de objetos y, si existe, realizar alguna acción en función de dónde se produjo la entrada táctil:

    * _Dentro de TouchImage_ : mostrar el texto `Touches Began` en una etiqueta y cambiar la imagen.
    * _Dentro de DoubleTouchImage_ : cambiar la imagen se muestra si el gesto era un doble punteo.
    * _Dentro de DragImage_ – establece una marca que indica que se ha iniciado la entrada táctil. El método `TouchesMoved` usará esta marca para determinar si `DragImage` debe desplazar por la pantalla o no, como veremos en el paso siguiente.

    El código anterior solo se ocupa un toque individual, siempre no queda ningún comportamiento si el usuario está moviendo su dedo en la pantalla. Para responder al movimiento, implemente `TouchesMoved` tal como se muestra en el código siguiente:

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

    Este método obtiene un `UITouch` objeto y, a continuación, comprueba que se produjo la entrada táctil. Si se produjo la entrada táctil en `TouchImage`, a continuación, el texto movido toques se muestra en la pantalla. 

    Si `touchStartedInside` es true, sabemos que el usuario tiene su dedo `DragImage` y se lo mueva. El código moverá `DragImage` cuando el usuario mueve el dedo hacia alrededor de la pantalla.

1. Es necesario controlar el caso cuando el usuario levanta el dedo sonará su fuera de la pantalla o iOS cancela el evento de toque. Para ello, se implementará `TouchesEnded` y `TouchesCancelled` tal como se muestra a continuación:

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
    
    Ambos métodos se restablecerán el `touchStartedInside` marca en false. `TouchesEnded` también mostrará `TouchesEnded` en la pantalla.

1. En este momento la pantalla táctil ejemplos ha finalizado. Observe que la pantalla cambia al interactuar con cada una de las imágenes, como se muestra en la captura de pantalla siguiente:
        
    [![](ios-touch-walkthrough-images/image4.png "La pantalla inicial de la aplicación")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "La pantalla después de que el usuario arrastra un botón")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

## <a name="gesture-recognizer-samples"></a>Ejemplos del reconocedor de gestos

El [sección anterior](#Touch_Samples) se ha mostrado cómo arrastrar un objeto alrededor de la pantalla mediante el uso de eventos de toque.
En esta sección, se permitirán deshacerme de los eventos de toque y muestran cómo utilizar los reconocedores de gestos siguientes:

-  El `UIPanGestureRecognizer` para arrastrar una imagen en torno a la pantalla.
-  El `UITapGestureRecognizer` para responder a derivaciones de tipo double en la pantalla.

Si ejecuta el [a partir de código de ejemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) y haga clic en el **ejemplos del reconocedor de gestos** botón, debería ver la siguiente pantalla:

 [![](ios-touch-walkthrough-images/image6.png "Esta pantalla muestra al hacer clic en el botón de ejemplos del reconocedor de gestos")](ios-touch-walkthrough-images/image6.png#lightbox)

Siga estos pasos para implementar los reconocedores de gestos:


1. Edite el archivo **GestureViewController.cs** y agregue la siguiente variable de instancia:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Necesitamos esta variable de instancia para realizar un seguimiento de la ubicación de la imagen anterior.
El reconocedor de movimiento panorámico usará el `originalImageFrame` para calcular el desplazamiento requerido para volver a dibujar la imagen en la pantalla.

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

    Este código crea instancias de un `UIPanGestureRecognizer` de instancia y lo agrega a una vista.
Tenga en cuenta que asignamos un destino para el gesto en el formulario del método `HandleDrag` : este método se proporciona en el paso siguiente.

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

    El código anterior se compruebe primero el estado del reconocedor de gestos y, a continuación, mover la imagen alrededor de la pantalla. Con este código en su lugar, el controlador ahora puede admitir arrastrar la imagen de una en torno a la pantalla.


1. Agregar un `UITapGestureRecognizer` que cambiará la imagen que se muestran en DoubleTouchImage. Agregue el método siguiente a la `GestureViewController` controlador:

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

    Este código es muy similar al código para el `UIPanGestureRecognizer` , pero en lugar de usar un delegado para un destino que estamos usando un `Action`. 

1. La última que necesitamos hacer es modificar `ViewDidLoad` para que llama a los métodos que acabamos de agregar. Cambiar ViewDidLoad por lo que es similar al código siguiente:

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

    Tenga en cuenta también que se inicialice el valor de `originalImageFrame`.


1. Ejecute la aplicación e interactuar con las dos imágenes.
Captura de pantalla siguiente es un ejemplo de estas interacciones:
    
    [![](ios-touch-walkthrough-images/image7.png "Esta captura de pantalla muestra una interacción de arrastrar")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Reconocedor de gestos personalizados

En esta sección se aplicará los conceptos de las secciones anteriores para crear un reconocedor de movimiento personalizada. El reconocedor de gestos personalizados le subclases `UIGestureRecognizer`y se reconoce cuando el usuario dibuja una "V" en la pantalla, a continuación, activar o desactivar un mapa de bits. Captura de pantalla siguiente es un ejemplo de esta pantalla:

 [![](ios-touch-walkthrough-images/image8.png "Reconocerá la aplicación cuando el usuario dibuja una \"V\" en la pantalla")](ios-touch-walkthrough-images/image8.png#lightbox)

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

    El método Reset se llama cuando el `State` propiedad cambia a `Recognized` o `Ended`. Este es el momento de restablecer ningún estado interno establecido en el reconocedor de gestos personalizados.
Ahora la clase puede empezar de cero próxima vez que el usuario interactúa con la aplicación y esté listo para volver a intentar reconocer el gesto.



1. Ahora que hemos definido un reconocedor de gestos personalizados (`CheckmarkGestureRecognizer`) editar el **CustomGestureViewController.cs** archivo y agregue las siguientes variables de instancia de dos:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Para crear una instancia y configurar nuestro reconocedor de gestos, agregue el método siguiente al controlador:

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

1. Editar `ViewDidLoad` para que llame `WireUpCheckmarkGestureRecognizer`, tal y como se muestra en el siguiente fragmento de código:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Ejecute la aplicación y, intente dibujar una "V" en la pantalla. Debería ver la imagen que se va a mostrar el cambio, como se muestra en las capturas de pantalla siguiente:
    
    [![](ios-touch-walkthrough-images/image9.png "El botón Comprobar")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "El botón desactivado")](ios-touch-walkthrough-images/image10.png#lightbox)



Los anteriores tres secciones demostradas diferentes formas de responder al tacto eventos en iOS: usar eventos de toque, los reconocedores de gestos integradas, o con un reconocedor de gestos personalizados.



## <a name="related-links"></a>Vínculos relacionados

- [iOS tocar inicio (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
