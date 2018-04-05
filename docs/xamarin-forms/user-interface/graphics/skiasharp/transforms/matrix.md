---
title: Transformaciones de matriz
description: Profundizar más en SkiaSharp transformaciones con la matriz de transformación versátil
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 6f7de8724a16e8c9c900123ce7da79d33b51a08c
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="matrix-transforms"></a>Transformaciones de matriz

_Profundizar más en SkiaSharp transformaciones con la matriz de transformación versátil_

Todas las transformaciones aplicadas a la `SKCanvas` objeto se consolidan en una única instancia de la [ `SKMatrix` ](https://developer.xamarin.com/api/type/SkiaSharp.SKMatrix/) estructura. Se trata de una matriz de transformación de 3 por 3 estándar parecida a las de todos los sistemas de gráficos 2D moderna.

Como ha visto, puede utilizar las transformaciones en SkiaSharp sin saber acerca de la transformación de matriz, pero la matriz de transformación es importante desde una perspectiva teórica y es fundamental cuando se usa transformaciones para modificar rutas de acceso o para controlar la característica de entrada táctil compleja, ambos que se muestran en este artículo y la siguiente.

![](matrix-images/matrixtransformexample.png "Un mapa de bits que se somete a una transformación afín")

La matriz de transformación actual que se aplica a la `SKCanvas` está disponible en cualquier momento mediante el acceso a solo lectura [ `TotalMatrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.TotalMatrix/) propiedad. Puede establecer una nueva matriz de transformación con el [ `SetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.SetMatrix/p/SkiaSharp.SKMatrix/) método y se puede restaurar esa matriz de transformación con valores predeterminados mediante una llamada a [ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix/).

El único otro `SKCanvas` miembro que funciona directamente con la transformación de matriz del lienzo es [ `Concat` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Concat/p/SkiaSharp.SKMatrix@/) que concatena dos matrices mediante la multiplicación de juntos.

La matriz de transformación de forma predeterminada es la matriz de identidad y consta de 1 en las celdas diagonales y en los demás lugares 0:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

Puede crear una matriz de identidad mediante el método estático [ `SKMatrix.MakeIdentity` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeIdentity()/) método:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

El `SKMatrix` constructor predeterminado no hace *no* devuelve una matriz de identidad. Devuelve una matriz con todas las celdas que se establecen en cero. No utilice la `SKMatrix` constructor a menos que se va a establecer esas celdas manualmente.

Cuando se SkiaSharp representa un objeto gráfico, cada punto (x, y) eficazmente se convierte en una matriz de 1 al 3 con un 1 en la tercera columna:

<pre>
| x  y  1 |
</pre>

Esta matriz de 1-por-3 representa un punto tridimensional con la coordenada Z establecida en 1. Hay razones matemáticas (se describe más adelante) por qué una transformación de matriz bidimensional requiere trabajo en tres dimensiones. Se puede considerar esta matriz 1 por 3 representa un punto en un sistema de coordenadas 3D, pero siempre en el plano 2D donde Z es igual a 1.

Esta matriz de 1-por-3, a continuación, se multiplica por la matriz de transformación y el resultado es el punto que se represente en el lienzo:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

Mediante la multiplicación de matrices estándar, los puntos convertidos son:

x' = x

y' = s

z' = 1

Es la transformación de forma predeterminada.

Cuando el `Translate` método se llama en el `SKCanvas` objeto, el `tx` y `ty` argumentos para el `Translate` método se convierten en las dos primeras celdas de la tercera fila de la matriz de transformación:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

La multiplicación ahora es como sigue:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Estas son las fórmulas de transformación:

x' = x + tx

y' = y + ty

Factores de escala tienen un valor predeterminado de 1. Cuando se llama a la `Scale` método en una nueva `SKCanvas` objeto, la matriz de transformación resultante contiene la `sx` y `sy` argumentos en las celdas diagonales:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

Las fórmulas de transformación son los siguientes:

x' = sx · x

y' = sy · y

La matriz de transformación después de llamar a `Skew` contiene los dos argumentos en las celdas adyacentes a los factores de escala de la matriz:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

Las fórmulas de transformación son:

x' = x + xSkew · y

y' = ySkew · x + y

Para una llamada a `RotateDegrees` o `RotateRadians` con un ángulo de α, la matriz de transformación es como sigue:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Estas son las fórmulas de transformación:

x' = cos(α) · x - sin(α) · y

y' = sin(α) · x - cos(α) · y

Una vez α 0 grados, es la matriz de identidad. Una vez α 180 grados, la matriz de transformación es como sigue:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Un giro de 180 grados equivale a voltear un objeto horizontalmente y verticalmente, que también se realiza mediante el establecimiento de los factores de escala de – 1.

Todos estos tipos de transformaciones se clasifican como *afín* transforma. Transformaciones afines nunca implican la tercera columna de la matriz, que permanece en los valores predeterminados de 0, 0 y 1. El artículo [transformaciones afines no](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md) describe las transformaciones no afines.

## <a name="matrix-multiplication"></a>Multiplicación de matrices

Una gran ventaja con el uso de la matriz de transformación es que las transformaciones compuestas pueden obtenerse mediante la multiplicación de matrices, que a menudo se hace referencia en la documentación de SkiaSharp como *concatenación*. Muchos de los métodos relacionados con la transformación de `SKCanvas` hacer referencia a "concatenación previa" o "pre-concat." Esto hace referencia al orden de multiplicación, que es importante porque la multiplicación de matrices no es conmutativa.

Por ejemplo, la documentación de la [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/) método dice que TI "Pre-concats la matriz actual con la traslación especificada," mientras la documentación para el [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/) método dice que TI "Pre-concats la matriz actual con la escala especificada."

Esto significa que la transformación especificada por la llamada al método es el multiplicador (el operando izquierdo) y la matriz de transformación actual es multiplicando (el operando derecho).

Suponga que `Translate` se denomina seguido `Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

El `Scale` transformación se multiplica por el `Translate` transformar para la matriz de transformación compuesta:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` se podría llamar antes `Translate` similar a la siguiente:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

En ese caso, se invierte el orden de la multiplicación, y los factores de escala se aplican eficazmente a los factores de conversión:

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Este es el `Scale` método con un punto de pivote:

```csharp
canvas.Scale(sx, sy, px, py);
```

Esto es equivalente a las siguientes llamadas de traducción y la escala:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Las tres matrices de transformación se multiplican en orden inverso de la aparecen de los métodos en el código:

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

### <a name="the-skmatrix-structure"></a>La estructura de SKMatrix

El `SKMatrix` estructura define nueve propiedades de lectura/escritura de tipo `float` correspondiente a las nueve celdas de la matriz de transformación:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` También define una propiedad denominada [ `Values` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix.Values/) de tipo `float[]`. Esta propiedad puede utilizarse para establecer u obtener los valores de una sola vez en el orden nueve `ScaleX`, `SkewX`, `TransX`, `SkewY`, `ScaleY`, `TransY`, `Persp0`, `Persp1`, y `Persp2`.

El `Persp0`, `Persp1`, y `Persp2` celdas se describen en el artículo de [transformaciones afines no](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Si estas celdas tienen sus valores predeterminados de 0, 0 y 1, la transformación se multiplica por un punto de coordenada similar al siguiente:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

x' = ScaleX · x + SkewX · y + TransX

y' = SkewX · x + ScaleY · y + TransY

z' = 1

Se trata de la transformación afín bidimensional completa. La transformación afín conserva líneas paralelas, lo que significa que un rectángulo nunca se transforma en algo distinto de un paralelogramo.

El `SKMatrix` estructura define varios métodos estáticos para crear `SKMatrix` valores. Estas devuelven `SKMatrix` valores:

- [`MakeTranslation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeTranslation/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/System.Single/System.Single/) con un punto de pivote
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/) para un ángulo en radianes
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/System.Single/System.Single/) para un ángulo en radianes con un punto de pivote
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/)
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/System.Single/System.Single/) con un punto de pivote
- [`MakeSkew`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeSkew/p/System.Single/System.Single/)

`SKMatrix` También define varios métodos estáticos que concatenan dos matrices, lo que significa para multiplicar en ellos. Estos métodos se denominan `Concat`, `PostConcat`, y `PreConcat`, y hay dos versiones de cada uno. Estos métodos no tienen ningún valor devuelto; en su lugar, hacen referencia a existente `SKMatrix` valores a través de `ref` argumentos. En el ejemplo siguiente, `A`, `B`, y `R` (para "resultado") son todas las `SKMatrix` valores.

Los dos `Concat` se denominan métodos similar al siguiente:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Estos operadores aritméticos realizan la multiplicación siguiente:

R = B × A

Los otros métodos tengan sólo dos parámetros. El primer parámetro es modificado y en la devolución de la llamada al método, contiene el producto de dos matrices. Los dos `PostConcat` se denominan métodos similar al siguiente:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Estas llamadas realizan las operaciones siguientes:

A = A × B

Los dos `PreConcat` métodos son similares:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Estas llamadas realizan las operaciones siguientes:

A = B × A

Las versiones de estas llamadas al método con todos los `ref` argumentos son ligeramente más eficaces de llamar a las implementaciones subyacentes, pero puede resultar confuso a alguien leer el código y suponiendo que cualquier elemento con un `ref` argumento es modificado por el método. Además, a menudo resulta conveniente pasar un argumento que es el resultado de uno de los `Make` métodos, por ejemplo:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Esto crea la siguiente matriz:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Se trata de la transformación de escala multiplicada por la transformación de traslación. En este caso concreto, el `SKMatrix` estructura proporciona un acceso directo con un método denominado [ `SetScaleTranslate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.SetScaleTranslate/p/System.Single/System.Single/System.Single/System.Single/):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Esta es una de la varias veces cuando resulta seguro utilizar el `SKMatrix` constructor. El `SetScaleTranslate` método establece todas las nueve celdas de la matriz. También es seguro utilizar el `SKMatrix` constructor con el método estático `Rotate` y `RotateDegrees` métodos:

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Estos métodos hacen lo *no* concatenar una transformación de giro a una transformación existente. Los métodos establecen todas las celdas de la matriz. Son funcionalmente idénticas a la `MakeRotation` y `MakeRotationDegrees` métodos salvo que no cree una instancia del `SKMatrix` valor.

Suponga que tiene un `SKPath` objeto que desea mostrar, pero prefiere que tenga una orientación ligeramente distinta, o un punto central diferente. Puede modificar todas las coordenadas de dicha ruta de acceso mediante una llamada a la [ `Transform` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Transform/p/SkiaSharp.SKMatrix/) método `SKPath` con un `SKMatrix` argumento. El **transformar de ruta de acceso** página muestra cómo hacerlo. El [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) clase referencias el `HendecagramPath` en un campo de un objeto pero con su constructor para aplicar una transformación a dicha ruta de acceso:

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

La `HendecagramPath` objeto tiene un centro en (0, 0), y los puntos de la estrella once amplían hacia afuera desde ese centro por 100 unidades en todas las direcciones. Esto significa que la ruta de acceso tiene coordenadas positivas y negativas. El **transformar de ruta de acceso** página prefiere trabajar con una estrella tres veces tan grande y con todas las coordenadas positivas. Además, no desee que un punto de la estrella para que apunte hacia arriba. Quiere en su lugar para un punto de la estrella para que apunte hacia abajo. (Dado que la estrella tiene once puntos, no puede tener ambos.) Para ello, la rotación de la estrella 360 grados dividido por 22.

El constructor genera una `SKMatrix` objeto de tres transformaciones independientes mediante la `PostConcat` método con el patrón siguiente, donde A, B y C son instancias de `SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Se trata de una serie de multiplicaciones sucesivas, por lo que el resultado es como sigue:

A × B × C

Las multiplicaciones consecutivas ayudan a comprender lo que hace cada transformación. La transformación de escala aumenta el tamaño de las coordenadas de la ruta de acceso en un factor de 3, por lo que las coordenadas oscilar entre –300 y 300. La transformación de giro gira la estrella alrededor de su origen. La transformación de traslación, a continuación, pasará por 300 píxeles hacia la derecha y hacia abajo, por lo que todas las coordenadas se convierten en positivo.

Hay otras secuencias que producen la misma matriz. Este es otro:

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Esto gira primero la ruta de acceso alrededor de su centro y, a continuación, lo traduce 100 píxeles a la derecha y hacia abajo de modo que todas las coordenadas son positivas. A continuación, se aumenta la estrella tamaño relativa a su esquina superior izquierda nueva, que es el punto (0, 0).

El `PaintSurface` controlador simplemente puede representar esta ruta de acceso:

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

Aparece en la esquina superior izquierda del lienzo:

[![](matrix-images/pathtransform-small.png "Captura de pantalla triple de la página de transformación de la ruta de acceso")](matrix-images/pathtransform-large.png#lightbox "Triple captura de pantalla de la página de transformación de la ruta de acceso")

El constructor de este programa aplica a la matriz a la ruta de acceso con la siguiente llamada:

```csharp
transformedPath.Transform(matrix);
```

La ruta de acceso *no* conservar esta matriz como una propiedad. En su lugar, la transformación se aplica a todas las coordenadas de la ruta de acceso. Si `Transform` se llama de nuevo, la transformación se aplica de nuevo, y es la única manera de poder volver a ella mediante la aplicación de otra matriz que deshace la transformación. Afortunadamente, la `SKMatrix` estructura define un [ `TryInverse` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.TryInvert/p/SkiaSharp.SKMatrix/) método que obtiene la matriz que invierte una matriz especificada:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

Se llama al método `TryInverse` porque no todas las matrices son puede invertir, pero no es probable que se usará para una transformación de gráficos una matriz no se puede invertir.

También puede aplicar una transformación de matriz a un `SKPoint` valor, una matriz de puntos, un `SKRect`, o incluso un número único dentro de su programa. El `SKMatrix` estructura es compatible con estas operaciones con una colección de métodos que comienzan con la palabra `Map`, como los siguientes:

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Si utiliza este último método, tenga en cuenta que el `SKRect` estructura no es capaz de representar un rectángulo girado. El método sólo tiene sentido una `SKMatrix` valor que representa la traducción y ajuste de escala.

### <a name="interactive-experimentation"></a>Experimentación interactivo

Es una manera de tener una idea de la transformación afín interactivamente moviendo tres esquinas de un mapa de bits alrededor de la pantalla y ver resultados de la transformación. Se trata de la idea que subyace el **Mostrar matriz afín** página. Esta página requiere dos otras clases que también se utilizan en otras demostraciones:

El [ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) clase muestra un círculo translúcido que puede arrastrarse alrededor de la pantalla. `TouchPoint` requiere que un `SKCanvasView` o un elemento que es un elemento primario de un `SKCanvasView` tienen la [ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) adjunta. Establezca la propiedad `Capture` en `true`. En el `TouchAction` controlador de eventos, el programa debe llamar a la `ProcessTouchEvent` método `TouchPoint` para cada `TouchPoint` instancia. El método devuelve `true` si el evento de entrada táctil se produjo en el punto de entrada táctil mover. Además, el `PaintSurface` controlador debe llamar a la `Paint` método en cada uno `TouchPoint` instancia, pasando al mismo el `SKCanvas` objeto.

`TouchPoint` muestra un comunes de forma que se puede encapsular un objeto visual SkiaSharp en una clase independiente. La clase puede definir propiedades para especificar las características del objeto visual y un método denominado `Paint` con un `SKCanvas` argumento puede interpretarlo.

El `Center` propiedad de `TouchPoint` indica la ubicación del objeto. Esta propiedad puede establecerse para inicializar la ubicación; los cambios de propiedad cuando el usuario arrastra el círculo alrededor del lienzo.

El **mostrar la página de matriz afín** también requiere la [ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) clase. Esta clase muestra las celdas de una `SKMatrix` objeto. Tiene dos métodos públicos: `Measure` para obtener las dimensiones de la matriz representada, y `Paint` para mostrarla. La clase contiene un `MatrixPaint` propiedad de tipo `SKPaint` que puede reemplazarse por un color o tamaño de fuente diferente.

El [ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) crea una instancia de archivo la `SKCanvasView` y adjunta un `TouchEffect`. El [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) archivo de código subyacente crea tres `TouchPoint` objetos y, a continuación, se establece a las posiciones correspondientes a tres de las esquinas de un mapa de bits que carga desde un incrustado recursos:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

Una matriz afín se define de forma única por tres puntos. Los tres `TouchPoint` objetos corresponden a las esquinas superior izquierda, superior derecha e inferior izquierda del mapa de bits. Dado que una matriz afín sólo es capaz de transformar un rectángulo en un paralelogramo, el cuarto punto está implícito en los otros tres. El constructor concluye con una llamada a `ComputeMatrix`, que calcula las celdas de una `SKMatrix` objeto de estos tres puntos.

El `TouchAction` llamadas del controlador de la `ProcessTouchEvent` método de cada `TouchPoint`. El `scale` valor convierte las coordenadas de Xamarin.Forms de píxeles:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Si cualquier `TouchPoint` se haya movido, a continuación, llama al método `ComputeMatrix` nuevo e invalida la superficie.

El `ComputeMatrix` método determina la matriz implicada en esos tres puntos. Llama a la matriz `A` transformaciones un rectángulo de cuadrados de un píxel en un paralelogramo en función de los tres puntos, mientras que la transformación de escala llama `S` escala el mapa de bits a un rectángulo de cuadrados de un píxel. La matriz compuesta es `S` × `A`:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
    {
        // Scale transform
        SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

        // Affine transform
        SKMatrix A = new SKMatrix
        {
            ScaleX = ptUR.X - ptUL.X,
            SkewY = ptUR.Y - ptUL.Y,
            SkewX = ptLL.X - ptUL.X,
            ScaleY = ptLL.Y - ptUL.Y,
            TransX = ptUL.X,
            TransY = ptUL.Y,
            Persp2 = 1
        };

        SKMatrix result;
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

Por último, el `PaintSurface` método representa el mapa de bits en función de dicha matriz, la matriz se muestra en la parte inferior de la pantalla y representa los puntos táctiles en las tres esquinas del mapa de bits:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

Cuando la página se carga por primera vez, mientras que las otras dos pantallas se muestran después de cierta manipulación en la pantalla de iOS siguiente muestra el mapa de bits:

[![](matrix-images/showaffinematrix-small.png "Captura de pantalla triple de la página de matriz afín mostrar")](matrix-images/showaffinematrix-large.png#lightbox "Triple captura de pantalla de la página de matriz afín mostrar")

Aunque parece como si los puntos táctiles arrastre las esquinas del mapa de bits, es solo una ilusión. La matriz que se calcula a partir de los puntos táctiles transforma el mapa de bits para que las esquinas coincidan con los puntos táctiles.

Resulta más natural para que los usuarios mover, cambiar el tamaño y girar los mapas de bits no arrastrando los vértices, pero mediante el uso de uno o dos dedos directamente en el objeto que se va a arrastrar, emulación táctil de acercar y giran. Este tema se trata en el artículo siguiente [manipulación táctil](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

### <a name="the-reason-for-the-3-by-3-matrix"></a>El motivo de la matriz de 3 por 3

Se puede esperar que un sistema de gráficos bidimensionales requiere sólo una matriz de transformación de 2 por 2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Esto funciona para el ajuste de escala, rotación y sesgado incluso, pero no es capaz de la más básica de transformaciones, que es la traducción.

El problema es que la matriz de 2 por 2 representa un *lineal* transformar en dos dimensiones. Una transformación lineal conserva algunas operaciones aritméticas básicas, pero una de las implicaciones es que una transformación lineal nunca modifica el punto (0, 0). Una transformación lineal imposibilita la traducción.

En tres dimensiones, una matriz de transformación lineal tiene el siguiente aspecto:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

La celda con la etiqueta `SkewXY` significa que el valor de la coordenada X en función de valores de Y sesga; la celda `SkewXZ` significa que el valor de la coordenada X en función de valores de Z; sesga y valores de sesgo de forma similar para los demás `Skew` celdas.

Es posible restringir esta matriz de transformación 3D en un plano bidimensional estableciendo `SkewZX` y `SkewZY` en 0, y `ScaleZ` en 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Si se dibujan los gráficos bidimensionales completamente en el plano en un espacio 3D donde Z es igual a 1, la multiplicación de transformación es similar al siguiente:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Todo permanece en el plano bidimensional donde Z es igual a 1, pero la `SkewXZ` y `SkewYZ` celdas convierte en factores de traducción bidimensional.

Se trata cómo una transformación lineal tridimensional actúa como una transformación no lineal bidimensional. (Por analogía, las transformaciones en los gráficos 3D se basan en una matriz de 4 por 4).

El `SKMatrix` estructura en SkiaSharp define las propiedades de la tercera fila:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

Los valores distintos de cero de `Persp0` y `Persp1` dar lugar a las transformaciones que mueven los objetos fuera del plano bidimensional donde Z es igual a 1. ¿Qué ocurre cuando esos objetos se desplaza al ese plano se trata en el artículo en [transformaciones afines no](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
