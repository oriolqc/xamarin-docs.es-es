---
title: 'Tutorial: usar táctil en Android'
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/09/2018
ms.openlocfilehash: 625ba800ce498f80c0344c67e26bd79360de4002
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34050564"
---
# <a name="walkthrough---using-touch-in-android"></a>Tutorial: usar táctil en Android

Permítanos vea cómo usar los conceptos de la sección anterior en una aplicación en funcionamiento. Se creará una aplicación con cuatro actividades. La primera actividad será un menú o un panel de control que se iniciará las otras actividades para demostrar las distintas API. Captura de pantalla siguiente muestra la actividad principal:

[![Captura de pantalla de ejemplo con Me toque el botón](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

La primera actividad, ejemplo Touch, mostrará cómo utilizar controladores de eventos para tocar las vistas. La actividad reconocedor de movimientos demostrará cómo subclase `Android.View.Views` y controlar los eventos, así como se muestra cómo controlar los gestos de acercar. La tercera y última actividad, **gesto personalizado**, se muestra cómo usar gestos personalizados. Para facilitar las cosas seguir y absorber, se podrá dividir este tutorial en secciones, con cada sección centrarse en una de las actividades.

## <a name="touch-sample-activity"></a>Actividad de muestra de entrada táctil

-   Abra el proyecto **TouchWalkthrough\_iniciar**. El **MainActivity** es establecidos en vaya &ndash; depende de nosotros implementar el comportamiento de entrada táctil en la actividad. Si ejecuta la aplicación y haga clic en **ejemplo táctil**, deben iniciar las siguientes actividades:

    [![Captura de pantalla de actividad con Touch comienza muestra](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Ahora que hemos confirmado que inicia la actividad, abra el archivo **TouchActivity.cs** y agregue un controlador para el `Touch` eventos de la `ImageView`:

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

Observe que en el código anterior que se tratan los `Move` y `Down` acción de la misma. Esto es porque, aunque el usuario no puede elevación su dedo el `ImageView`, puede mover o la presión ejercida por el usuario puede cambiar. Estos tipos de cambios generará un `Move` acción.

Cada vez que el usuario los últimos retoques el `ImageView`, `Touch` se generará el evento y el controlador mostrará el mensaje **Touch comienza** en la pantalla, como se muestra en la captura de pantalla siguiente:

[![Captura de pantalla de actividad táctil se inicie](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Siempre y cuando el usuario toca el `ImageView`, **Touch comienza** se mostrará en el `TextView`. Cuando el usuario ya no se usa el `ImageView`, el mensaje **Touch finaliza** se mostrará en el `TextView`, tal y como se muestra en la captura de pantalla siguiente:

[![Captura de pantalla de actividad con Touch finaliza](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Actividad de reconocedor de gestos

Ahora permite implementar la actividad reconocedor de movimientos. Esta actividad muestra cómo arrastrar una vista en torno a la pantalla y se muestra una manera de implementar el gesto de acercamiento.

-   Agregar una nueva actividad a la aplicación llamada `GestureRecognizer`.
    Edite el código para esta actividad para que sea similar el siguiente código:

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

-   Agregar un nuevo Android ver al proyecto y asígnele el nombre `GestureRecognizerView`. Agregue las siguientes variables para esta clase:

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

-   Para dibujar la imagen en nuestra actividad, es necesario reemplazar el `OnDraw` método de la clase de vista, tal como se muestra en el siguiente fragmento de código. Este código se moverá el `ImageView` a la posición especificada por `_posX` y `_posY` así como cambiar el tamaño de la imagen según el factor de escala:

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

-   A continuación deberá actualizar la variable de instancia `_scaleFactor` como el usuario pinche el `ImageView`. Se agregará una clase denominada `MyScaleListener`. Esta clase realizará escuchas para los eventos de escala que se generará Android cuando el usuario pinche el `ImageView`.
    Agregue la siguiente clase interna para `GestureRecognizerView`. Esta clase es una `ScaleGesture.SimpleOnScaleGestureListener`. Esta clase es una clase para comodidad que los agentes de escucha pueden crear subclases cuando esté interesado en un subconjunto de gestos:

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

            _iconview.Invalidate();
            return true;
        }
    }
    ```

-   El siguiente método se debe invalidar en `GestureRecognizerView` es `OnTouchEvent`. El código siguiente muestra la implementación completa de este método. Hay una gran cantidad de código en este caso, por lo que permite tardar un minuto, y observe que está sucediendo aquí. Lo primero que el método no es escalar el icono si es necesario &ndash; Esto se controla mediante una llamada a `_scaleDetector.OnTouchEvent`. A continuación intente averiguar qué acción llama a este método:

    - Si el usuario toca la pantalla con, se registran las posiciones X e Y y el identificador del primer puntero que tocar la pantalla.

    - Si el usuario mueve su táctil en la pantalla, a continuación, se averiguar hasta qué punto el usuario desplaza el puntero.

    - Si el usuario ha solucionado su dedo fuera de la pantalla, a continuación, se dejará los movimientos de seguimiento.

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

-   Ahora ejecute la aplicación e inicie la actividad reconocedor de movimientos.
    Cuando se inicia la pantalla debe ser similar a la captura de pantalla siguiente:

    [![Pantalla de inicio de reconocedor de movimiento con icono de Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Ahora pulse en el icono y arrastrarla alrededor de la pantalla. Pruebe el gesto de gesto de acercamiento. En algún momento la pantalla puede ser algo parecido a la captura de pantalla siguiente:

    [![Icono de mover movimientos por la pantalla](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

En este punto debe disponer de un pat en la parte posterior: acaba de implementar gesto de acercamiento en una aplicación Android. Tómese un descanso rápido y permite pasar a la tercera y última actividad en este tutorial &ndash; con gestos personalizados.

## <a name="custom-gesture-activity"></a>Actividad de gestos personalizados

La pantalla final de este tutorial usará gestos personalizados.

Para los fines de este tutorial, la biblioteca de movimientos ya creada con la herramienta de movimiento y agrega al proyecto en el archivo **recursos/sin formato/movimientos**. Con este bit de fuera de la vista de limpieza, permite obtener en la actividad final en el tutorial.

-   Agregue un archivo de diseño denominado **personalizado\_gesto\_layout.axml** al proyecto con el siguiente contenido. El proyecto ya tiene todas las imágenes en el **recursos** carpeta:

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

-   Editar el `OnCreate` método de esta actividad, por lo que TI es similar al código siguiente. Permite realizar un minuto para explicar lo que está sucediendo en este código. Lo primero que hacemos es crear una instancia de un `GestureOverlayView` y establézcalo como la vista de la raíz de la actividad.
    También se asigna un controlador de eventos para el `GesturePerformed` eventos de `GestureOverlayView`. A continuación se aumentar el archivo de diseño que creó anteriormente y agregarlo como una vista secundaria de la `GestureOverlayView`. El paso final consiste en inicializar la variable `_gestureLibrary` y cargue el archivo de movimientos de los recursos de aplicación. Si no se puede cargar el archivo de movimientos por alguna razón, no hay mucho esta actividad puede hacer, por lo que es apagado:

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

-   Lo último que es necesario para implementar el método `GestureOverlayViewOnGesturePerformed` tal y como se muestra en el siguiente fragmento de código. Cuando el `GestureOverlayView` detecta un movimiento, vuelve a llamar a este método. Lo primero que se intenta obtener un `IList<Prediction>` objetos que coinciden con el movimiento mediante una llamada a `_gestureLibrary.Recognize()`. Utilizamos un poco de LINQ para obtener la `Prediction` que tiene la máxima puntuación para el movimiento.

    Si no hay ninguna coincidencia de gestos con un valor alto suficiente puntuación, a continuación, el controlador de eventos se cierra sin hacer nada. De lo contrario se compruebe el nombre de la predicción y cambia la imagen que se muestra según el nombre de los gestos:

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

-   Ejecute la aplicación e iniciar la actividad personalizada reconocedor de movimientos. Debería ser similar a la captura de pantalla siguiente:

    [![Captura de pantalla con comprobar mi imagen](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Ahora se dibuja una marca de verificación en la pantalla y el mapa de bits que se muestra debe ser similar a la se muestra en las capturas de pantalla siguiente:

    [![Se reconoce la marca de verificación dibujado, marca de verificación](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    Por último, puede dibujar un garabato en la pantalla. La casilla de verificación debería cambiar a la imagen original tal y como se muestra en estas capturas de pantalla:

    [![Scribble en la pantalla, la imagen original se muestra](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Ahora tiene una descripción de cómo integrar táctiles y los gestos en una aplicación Android con Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Android Touch iniciar (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
