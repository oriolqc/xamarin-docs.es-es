---
title: Manipulaciones táctil
description: Matriz de uso transforma para implementar arrastrar táctil, aprietan y rotación
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 1fbc9826b9edd3d4c8f7e4b47c3ea835d5625343
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="touch-manipulations"></a>Manipulaciones táctil

_Matriz de uso transforma para implementar arrastrar táctil, aprietan y rotación_

En entornos de multitoque como los dispositivos móviles, los usuarios normalmente usan los dedos para manipular objetos en la pantalla. Movimientos comunes como un arrastrar un solo dedo y un gesto del dedo dos pueden mover y escalar objetos o incluso intercambiarlos. Estos movimientos generalmente se implementan con matrices de transformación, y este artículo muestra cómo hacerlo.

![](touch-images/touchmanipulationsexample.png "Un mapa de bits sujeto a la traducción, ajuste de escala y giro")

## <a name="manipulating-one-bitmap"></a>Manipular un mapa de bits

El **manipulación táctil** página muestra las manipulaciones de entrada táctil en un mapa de bits única.
Este ejemplo hace uso del efecto de seguimiento de entrada táctil presentado en el artículo [invocar eventos de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Otros archivos son compatibles con el **manipulación táctil** página. La primera es la [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumeración, que indica los distintos tipos de manipulación táctil implementada por el código podrá ver:

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` es un arrastre de un solo dedo que se implementa con la traducción. Todas las opciones siguientes también incluyen panorámica pero implican dos dedos: `IsotropicScale` es una operación de acercar los dedos que da como resultado el objeto que escala equitativamente en las direcciones horizontal y verticales. `AnisotropicScale` permite escalar distintas.

El `ScaleRotate` opción es para un dedo dos escalado y la rotación. Ajuste de escala es isótropo. Implementación de rotación de dos dedos con escalado anisotrópico es problemático porque los movimientos del dedo son esencialmente el mismo.

El `ScaleDualRotate` opción agrega rotación de un solo dedo. Cuando un solo dedo arrastra el objeto, el objeto arrastrado en primer lugar se gira alrededor de su centro para que el centro del objeto que se alinee con el vector de arrastrar.

El [ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) archivo incluye una `Picker` con los miembros de la `TouchManipulationMode` enumeración:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>None</x:String>
                <x:String>PanOnly</x:String>
                <x:String>IsotropicScale</x:String>
                <x:String>AnisotropicScale</x:String>
                <x:String>ScaleRotate</x:String>
                <x:String>ScaleDualRotate</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>

        <Grid BackgroundColor="White"
              Grid.Row="1">

            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

Hacia la parte inferior es un `SKCanvasView` y un `TouchEffect` adjunto a la celda única `Grid` que lo incluye.

El [ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) archivo de código subyacente tiene una `bitmap` campo pero no es de tipo `SKBitmap`. El tipo es `TouchManipulationBitmap` (una clase que se verá en breve):

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            SKBitmap bitmap = SKBitmap.Decode(skStream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

El constructor crea un `TouchManipulationBitmap` objeto, pasando al constructor un `SKBitmap` obtenido de un recurso incrustado. El constructor concluye estableciendo la `Mode` propiedad de la `TouchManager` propiedad de la `TouchManipulationBitmap` objeto a un miembro de la `TouchManipulationMode` enumeración.

El `SelectedIndexChanged` controlador para la `Picker` también establece este `Mode` propiedad:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            TouchManipulationMode mode;
            Enum.TryParse(picker.Items[picker.SelectedIndex], out mode);
            bitmap.TouchManager.Mode = mode;
        }
    }
    ...
}
```

El `TouchAction` controlador de la `TouchEffect` crea una instancia en las llamadas de archivo XAML dos métodos de `TouchManipulationBitmap` denominado `HitTest` y `ProcessTouchEvent`:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Si el `HitTest` método `true` &mdash; lo que significa que un dedo toca la pantalla dentro del área ocupada por el mapa de bits &mdash; , a continuación, el identificador de entrada táctil se agrega a la `TouchIds` colección. Este identificador representa la secuencia de eventos de entrada táctil para ese dedo hasta que levante el dedo desde la pantalla. Si varios dedos tocan el mapa de bits, la `touchIds` colección contiene un identificador de contacto para cada dedo.

El `TouchAction` controlador también llama el `ProcessTouchEvent` clase `TouchManipulationBitmap`. Aquí es donde algunas (aunque no todos) de la entrada táctil real se produce el procesamiento.

El [ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) clase es una clase contenedora para `SKBitmap` que contiene el código para representar el mapa de bits y procesar los eventos de toque. Funciona junto con más generalizada código en un `TouchManipulationManager` clase (que verá en breve).

El `TouchManipulationBitmap` constructor guarda el `SKBitmap` y crea instancias de dos propiedades, el `TouchManager` propiedad de tipo `TouchManipulationManager` y `Matrix` propiedad de tipo `SKMatrix`:

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

Esto `Matrix` propiedad es la transformación acumulada resultante de la actividad de entrada táctil. Como verá, cada evento de entrada táctil se resuelve en una matriz, que, a continuación, se concatena con el `SKMatrix` valor almacenado por la `Matrix` propiedad.

El `TouchManipulationBitmap` objeto dibuja a sí mismo su `Paint` método. El argumento es un `SKCanvas` objeto. Esto `SKCanvas` podrían tener ya una transformación aplicada a él, por lo que la `Paint` método concatena el `Matrix` propiedad asociado con el mapa de bits a la transformación existente y restaura el lienzo cuando haya terminado:

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

El `HitTest` método `true` si el usuario toca la pantalla en un punto dentro de los límites del mapa de bits. Como el usuario manipula el mapa de bits, el mapa de bits se puede girar, o incluso (a través de una combinación de rotación y escala anisotrópico) esté en la forma de un paralelogramo. Podría temen que el `HitTest` método debe implementar en ese caso geometría analítica bastante compleja.

Sin embargo, un acceso directo está disponible:

Determinar si se encuentra un punto dentro de los límites de un rectángulo transformado es igual a determinar si un punto transformado inverso se encuentra dentro de los límites del rectángulo sin transformar. Que es un cantidad de cálculo sea más fácil y puede utilizar la cómoda `Contains` método definido por `SKRect`:

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

El segundo método público en `TouchManipulationBitmap` es `ProcessTouchEvent`. Cuando se llama a este método, ya se determinó que el evento de entrada táctil pertenece a este mapa de bits determinada. El método no mantiene un diccionario de [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objetos, que es simplemente el punto anterior y el nuevo punto de cada dedo:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Este es el diccionario y la `ProcessTouchEvent` propio método:

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

En el `Moved` y `Released` eventos, las llamadas al método `Manipulate`. En estos momentos, la `touchDictionary` contiene uno o varios `TouchManipulationInfo` objetos. Si el `touchDictionary` contiene un elemento, es probable que la `PreviousPoint` y `NewPoint` valores no son iguales y que representan el movimiento de un dedo. Si varios dedos están tocando el mapa de bits, el diccionario contiene más de un elemento, pero solo uno de estos elementos tiene distintos `PreviousPoint` y `NewPoint` valores. El resto tienen las mismas `PreviousPoint` y `NewPoint` valores.

Esto es importante: el `Manipulate` método puede asumir que está procesando el movimiento de un solo dedo. En el momento de la llamada a ninguno de los otros dedos es móvil, y si realmente desea mover (ya que es probable que), se procesarán los movimientos en las futuras llamadas a `Manipulate`.

El `Manipulate` método la primera copia del diccionario en una matriz para su comodidad. Omite cualquier cosa que no sea el primero de dos entradas. Si más de dos dedos intentan manipular el mapa de bits, los demás se omiten. `Manipulate` es el último miembro de `TouchManipulationBitmap`:

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

Si el mapa de bits, es la manipulación de un solo dedo `Manipulate` llamadas el `OneFingerManipulate` método de la `TouchManipulationManager` objeto. Para dos dedos llama `TwoFingerManipulate`. Los argumentos a estos métodos son los mismos: el `prevPoint` y `newPoint` argumentos representan el dedo que se mueven. Pero la `pivotPoint` argumento es diferente para las dos llamadas:

Para la manipulación de un solo dedo, el `pivotPoint` es el centro del mapa de bits. Esto es para permitir la rotación de un solo dedo. Para la manipulación de dos dedos, el evento indica que el movimiento de un solo dedo, por lo que el `pivotPoint` es el dedo que no se mueve.

En ambos casos, `TouchManipulationManager` devuelve un `SKMatrix` valor, que el método se concatena con el actual `Matrix` propiedad que `TouchManipulationPage` utiliza para representar el mapa de bits.

`TouchManipulationManager` se ha generalizado y no se utiliza ningún otro archivo excepto `TouchManipulationMode`. Es posible que pueda utilizar esta clase sin cambios en sus propias aplicaciones. Define una única propiedad de tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


Sin embargo, probablemente deseará evitar la `AnisotropicScale` opción. Es muy fácil con esta opción para manipular el mapa de bits para que uno de los factores de escala se convierte en cero. Esto hace que el mapa de bits desaparecen de la vista, nunca para devolver. Si realmente necesita escalado anisotrópico, desea mejorar la lógica para evitar resultados no deseados.

`TouchManipulationManager` hace uso de los vectores, pero dado que no hay ningún `SKVector` estructura en SkiaSharp, `SKPoint` se utiliza en su lugar. `SKPoint` admite que el operador de resta y el resultado se pueden tratar como un vector. La lógica específica de vector solo que deba agregarse es un `Magnitude` cálculo:

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

Cada vez que se ha seleccionado la rotación, tanto los métodos de manipulación de un solo dedo y dos dedo controlen la rotación primero. Si se detecta cualquier rotación, se quita eficazmente el componente de rotación. Lo que permanece se interpreta como la panorámica y el ajuste de escala.

Este es el `OneFingerManipulate` método. Si no se habilitó la rotación de un solo dedo, la lógica es sencilla &mdash; simplemente utiliza el punto anterior y el punto nuevo para crear un vector con el nombre `delta` que corresponde exactamente a la traducción. Con un solo dedo rotación habilitada, el método utiliza ángulos desde el punto de pivote (el centro del mapa de bits) en el punto anterior y nuevo punto para construir una matriz de rotación:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

En el `TwoFingerManipulate` (método), el punto de pivote es la posición del dedo que no se mueve en este caso particular táctil. La rotación es muy similar a la rotación de un solo dedo y, a continuación, se denomina el vector `oldVector` (según el punto anterior) se ajusta de la rotación. El movimiento restante se interpreta como el ajuste de escala:

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

Observará que no hay ninguna conversión explícita en este método. Sin embargo, tanto el `MakeRotation` y `MakeScale` métodos se basan en el punto de pivote, y que incluya una conversión implícita. Si usa dos dedos en el mapa de bits y arrastrarlos en la misma dirección, `TouchManipulation` obtendrá una serie de eventos de toque alternando entre los dos dedos. Como cada dedo se desplaza en relación con los demás, ajuste de escala o la rotación resultados, pero es negada por el movimiento del otro dedo y el resultado es traducción.

La única parte restante de la **manipulación táctil** página es la `PaintSurface` controlador en el `TouchManipulationPage` archivo de código subyacente. En este caso, el `Paint` método de la `TouchManipulationBitmap`, que aplica la matriz que representa la actividad táctil acumulado:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

El `PaintSurface` controlador concluye mostrando un `MatrixDisplay` objeto muestra la matriz de entrada táctil acumulado:

[![](touch-images/touchmanipulation-small.png "Captura de pantalla triple de la página de manipulación táctil")](touch-images/touchmanipulation-large.png#lightbox "Triple captura de pantalla de la página de manipulación táctil")

## <a name="manipulating-multiple-bitmaps"></a>Manipular varios mapas de bits

Una de las ventajas de aislar el código de procesamiento de entrada táctil en clases como `TouchManipulationBitmap` y `TouchManipulationManager` es la capacidad de volver a usar estas clases en un programa que permite al usuario manipular varios mapas de bits.

El **vista de mapa de bits de dispersión** página muestra cómo hacerlo. En lugar de definir un campo de tipo `TouchManipulationBitmap`, [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) clase define un `List` de objetos de mapa de bits:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                using (SKManagedStream skStream = new SKManagedStream(stream))
                {
                    SKBitmap bitmap = SKBitmap.Decode(skStream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

El constructor se carga en todos los mapas de bits disponibles como recursos incrustados y se agregarán a la `bitmapCollection`. Tenga en cuenta que la `Matrix` propiedad se inicializa en cada `TouchManipulationBitmap` de objeto, por lo que se calcula el desplazamiento de las esquinas superior izquierda de cada mapa de bits por 100 píxeles.

La `BitmapScatterView` página también es necesario controlar los eventos de entrada táctil para varios mapas de bits. En lugar de definir una `List` de touch Id. de manipula actualmente `TouchManipulationBitmap` objetos, este programa requiere un diccionario:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Observe cómo la `Pressed` lógica recorre el `bitmapCollection` en orden inverso. Los mapas de bits a menudo superponen entre sí. Los mapas de bits más adelante en la colección se encuentran visualmente encima de los mapas de bits anteriormente en la colección. Si hay varios mapas de bits en el dedo que presiona en la pantalla, lo más alto debe ser la que se manipula mediante ese dedo.

Observe también que el `Pressed` lógica mueve ese mapa de bits al final de la colección para que visualmente se mueve a la parte superior de la pila de otros mapas de bits.

En el `Moved` y `Released` eventos, el `TouchAction` llamadas del controlador de la `ProcessingTouchEvent` método `TouchManipulationBitmap` al igual que el programa anterior.

Por último, el `PaintSurface` llamadas del controlador de la `Paint` método de cada `TouchManipulationBitmap` objeto:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

El código recorre en iteración la colección y muestra la pila de mapas de bits desde el principio de la colección al final:

[![](touch-images/bitmapscatterview-small.png "Captura de pantalla triple de la página de vista de mapa de bits de dispersión")](touch-images/bitmapscatterview-large.png#lightbox "Triple captura de pantalla de la página de vista de mapa de bits de dispersión")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Invocación de eventos de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
