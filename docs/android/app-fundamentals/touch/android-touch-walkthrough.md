---
title: 'Tutorial: uso táctil en Android'
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/09/2018
ms.openlocfilehash: d379630e3b7fa2b42bd9530e1dccd75e9634dd2f
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935532"
---
# <a name="walkthrough---using-touch-in-android"></a>Tutorial: uso táctil en Android

Nos gustaría ver cómo usar los conceptos de la sección anterior en una aplicación de trabajo. Se creará una aplicación con cuatro actividades. La primera actividad será un menú o un panel de control que se iniciará las otras actividades para demostrar las distintas API. Captura de pantalla siguiente muestra la actividad principal:

[![Captura de pantalla de ejemplo con Me toque el botón](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

La primera actividad, ejemplo de Touch, mostrará cómo utilizar controladores de eventos para tocar las vistas. La actividad del reconocedor de gestos demostrará cómo crear subclases de `Android.View.Views` y controlar los eventos, así como se muestra cómo controlar los gestos pinch. La tercera y última actividad, **gesto personalizado**, le muestran cómo usar gestos personalizados. Para facilitar las cosas a seguir y absorber, dividiremos en este tutorial en secciones, con cada sección centrándose en una de las actividades.

## <a name="touch-sample-activity"></a>Actividad de ejemplo de Touch

-   Abra el proyecto **TouchWalkthrough\_iniciar**. El **MainActivity** está listo para ir &ndash; depende de nosotros para implementar el comportamiento de toque en la actividad. Si ejecuta la aplicación y haga clic en **Touch ejemplo**, se debe iniciar la actividad siguiente:

    [![Captura de pantalla de actividad con Touch comienza a mostrar](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Ahora que hemos confirmado que se inicia la actividad, abra el archivo **TouchActivity.cs** y agregue un controlador para el `Touch` eventos de la `ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   A continuación, agregue el método siguiente a **TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

Tenga en cuenta en el código anterior que tratamos la `Move` y `Down` acción de la misma. Esto es porque, aunque el usuario no puede levante su dedo el `ImageView`, pueden desplazarse o la presión ejercida por el usuario puede cambiar. Estos tipos de cambios se generarán un `Move` acción.

Cada vez que los toques del usuario la `ImageView`, el `Touch` , se generará el evento y el controlador mostrará el mensaje **comienza a tocar** en la pantalla, como se muestra en la captura de pantalla siguiente:

[![Captura de pantalla de actividad con comienza a tocar](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Siempre y cuando el usuario toca el `ImageView`, **comienza a tocar** se mostrará en el `TextView`. Cuando el usuario ya no esté tocando la `ImageView`, el mensaje **Touch finaliza** se mostrará en el `TextView`, tal y como se muestra en la captura de pantalla siguiente:

[![Captura de pantalla de actividad con Touch finaliza](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Actividad del reconocedor de gestos

Ahora permite implementar la actividad del reconocedor de gestos. Esta actividad demostrará cómo arrastrar una vista en torno a la pantalla y muestran una forma de implementar acercar para alejar.

-   Agregue una nueva actividad a la aplicación llamada `GestureRecognizer`.
    Edite el código para esta actividad para que se parezca que el código siguiente:

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

-   Agregar una nueva Android ver al proyecto y asígnele el nombre `GestureRecognizerView`. Agregue las siguientes variables para esta clase:

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

-   Agregue el siguiente constructor a `GestureRecognizerView`. Este constructor se agregará un `ImageView` a nuestra actividad. En este punto el código todavía no se compilará &ndash; es necesario crear la clase `MyScaleListener` que le ayudará a cambiar el tamaño de la `ImageView` cuando el usuario pinche:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   Para dibujar la imagen en nuestra actividad, es necesario reemplazar el `OnDraw` método de la clase de vista, como se muestra en el siguiente fragmento de código. Este código se moverá el `ImageView` a la posición especificada por `_posX` y `_posY` , así como cambiar el tamaño de la imagen de según el factor de escala:

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

-   A continuación deberá actualizar la variable de instancia `_scaleFactor` como el usuario pinche el `ImageView`. Agregamos una clase denominada `MyScaleListener`. Esta clase realizará escuchas para los eventos de escala que se generará Android cuando el usuario pinche el `ImageView`.
    Agregue la siguiente clase interna para `GestureRecognizerView`. Esta clase es un `ScaleGesture.SimpleOnScaleGestureListener`. Esta clase es una clase de conveniencia que los agentes de escucha pueden crear subclases cuando esté interesado en un subconjunto de gestos:

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _view.Invalidate();
            return true;
        }
    }
    ```

-   El siguiente método se debe invalidar en `GestureRecognizerView` es `OnTouchEvent`. El código siguiente muestra la implementación completa de este método. Hay una gran cantidad de código por lo que vamos aquí, tómese un minuto y observe lo que sucede aquí. Lo primero que este método no es escalar el icono si es necesario &ndash; Esto se controla mediante una llamada a `_scaleDetector.OnTouchEvent`. A continuación intente averiguar qué acción se llama a este método:

    - Si el usuario toca la pantalla con, se debe registrar las posiciones X e Y y el identificador del primer puntero que toca la pantalla.

    - Si el usuario mueve su toque la pantalla, a continuación, averiguar hasta qué punto el usuario mueve el puntero.

    - Si el usuario eleva su dedo fuera de la pantalla, a continuación, se dejará de seguimiento de los gestos.

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

-   Ahora ejecute la aplicación e inicie la actividad del reconocedor de gestos.
    Cuando se inicia la pantalla debe ser similar a la captura de pantalla siguiente:

    [![Pantalla de inicio de reconocedor de gestos con icono de Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Ahora toque el icono y arrastrarla alrededor de la pantalla. Pruebe el gesto pinch a zoom. En algún momento, la pantalla puede ser algo parecido a la captura de pantalla siguiente:

    [![Icono de mover los gestos en torno a la pantalla](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

En este momento debería disponer de un pat en la parte posterior: acaba de implementar pinch a zoom en una aplicación de Android. Tómese un descanso rápido y permite pasar a la tercera y última actividad en este tutorial &ndash; mediante gestos personalizados.

## <a name="custom-gesture-activity"></a>Actividad de gestos personalizados

La pantalla final de este tutorial usará gestos personalizados.

Para los fines de este tutorial, la biblioteca de gestos ya se ha creado con herramienta gesto y agrega al proyecto en el archivo **recursos/sin formato/gestos**. Con este poco de limpieza de la vista, permite obtener con la actividad final en el tutorial.

-   Agregue un archivo de diseño denominado **personalizado\_gesto\_layout.axml** al proyecto con el siguiente contenido. El proyecto ya tiene todas las imágenes de la **recursos** carpeta:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

-   A continuación, agregue una nueva actividad al proyecto y asígnele el nombre `CustomGestureRecognizerActivity.cs`. Agregue dos variables de instancia a la clase, como se muestra en las siguientes dos líneas de código:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   Editar el `OnCreate` método de esta actividad, por lo que TI es similar al código siguiente. Permite tomar un minuto para explicar lo que está ocurriendo en este código. Lo primero que hacemos es crear una instancia de un `GestureOverlayView` y establézcalo como la vista de la raíz de la actividad.
    También asignamos un controlador de eventos para el `GesturePerformed` událostí `GestureOverlayView`. A continuación se infle el archivo de diseño que se creó anteriormente y agregarlo como una vista secundaria de la `GestureOverlayView`. El último paso consiste en inicializar la variable `_gestureLibrary` y cargar el archivo de movimientos de los recursos de aplicación. Si no se puede cargar el archivo de gestos por alguna razón, no hay mucho pueda hacer esta actividad, por lo que es de cierre:

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

-   El paso final es necesario para implementar el método `GestureOverlayViewOnGesturePerformed` tal como se muestra en el siguiente fragmento de código. Cuando el `GestureOverlayView` detecta un gesto, vuelve a llamar a este método. Lo primero que se trate de conseguir un `IList<Prediction>` objetos que coinciden con el gesto mediante una llamada a `_gestureLibrary.Recognize()`. Usamos un poco de LINQ para obtener el `Prediction` que tiene la máxima puntuación para el movimiento.

    Si no se produjo ninguna coincidencia suficiente puntuación de gestos con un valor alto, entonces el controlador de eventos se cierra sin hacer nada. De lo contrario, compruebe el nombre de la predicción y cambiar la imagen que se muestra según el nombre del gesto:

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

-   Ejecute la aplicación e inicie la actividad personalizada reconocedor de gestos. Debe tener un aspecto similar a la captura de pantalla siguiente:

    [![Captura de pantalla con comprobar mi imagen](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Ahora se dibuja una marca de verificación en la pantalla y el mapa de bits que se muestra debe ser similar a la se muestra en las capturas de pantalla siguiente:

    [![Marca de verificación dibujado, marca de verificación se reconoce](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    Por último, puede dibujar un garabato en la pantalla. La casilla de verificación debería cambiar a la imagen original, como se muestra en estas capturas de pantalla:

    [![Se muestra en la pantalla, la imagen original Scribble de](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Ahora tiene una comprensión de cómo integrar tacto y gestos en una aplicación de Android con Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Android tocar inicio (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
