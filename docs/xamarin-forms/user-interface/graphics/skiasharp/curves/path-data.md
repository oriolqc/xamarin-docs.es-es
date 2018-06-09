---
title: Datos de ruta de acceso SVG en SkiaSharp
description: En este artículo se explica cómo definir rutas de acceso de SkiaSharp mediante cadenas de texto en el formato de gráficos vectoriales escalables y se muestra cómo hacerlo con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: charlespetzold
ms.author: chape
ms.date: 05/24/2017
ms.openlocfilehash: 0453374c59c3b12842b7fb1524cc150329d84b7f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243979"
---
# <a name="svg-path-data-in-skiasharp"></a>Datos de ruta de acceso SVG en SkiaSharp

_Definir rutas de acceso mediante cadenas de texto en el formato de gráficos vectoriales escalables_

La `SKPath` clase es compatible con la definición de objetos de ruta de acceso completa de las cadenas de texto en un modelo establecido por la especificación de Scalable Vector Graphics (SVG). Más adelante en este artículo verá cómo se puede representar una ruta de acceso completa de este en una cadena de texto:

![](path-data-images/pathdatasample.png "Una ruta de acceso de ejemplo definida con los datos de ruta de acceso SVG")

SVG es un lenguaje de programación de páginas web de gráficos basados en XML. Como SVG debe permitir que las rutas de acceso que deben definirse en el marcado en lugar de una serie de llamadas de función, el estándar SVG incluye una manera muy concisa de especificar una ruta de acceso completa de gráficos como una cadena de texto.

Dentro de SkiaSharp, este formato se denomina "Ruta de acceso SVG-data." El formato también se admite en entornos de programación basadas en XAML de Windows, incluidos Windows Presentation Foundation y la plataforma Universal de Windows, donde se le conoce como el [sintaxis de ruta de acceso de marcado](https://msdn.microsoft.com/library/ms752293%28v=vs.110%29.aspx) o [mover y dibuje la sintaxis de comandos](/windows/uwp/xaml-platform/move-draw-commands-syntax/). También pueden actuar como un formato de intercambio de gráficos vectoriales, especialmente en archivos basados en texto, como XML.

SkiaSharp define dos métodos con las palabras `SvgPathData` en sus nombres:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

El método estático [ `ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) método convierte una cadena en un `SKPath` objeto, mientras que [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) convierte un `SKPath` objeto en una cadena.

Ésta es una cadena SVG para una estrella de cinco puntas centrada en el punto (0, 0) con un radio de 100:

```csharp
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Las letras son comandos que crean un `SKPath` objeto. `M` indica un `MoveTo` llamar, `L` es `LineTo`, y `Z` es `Close` para cerrar un contorno. Cada par de números proporciona una coordenada X e Y de un punto. Tenga en cuenta que el `L` comando va seguido de varios puntos separados por comas. En una serie de coordenadas y puntos, comas y espacios en blanco se tratan de forma idéntica. Algunos programadores prefieren poner comas entre las coordenadas X e Y, en lugar de entre los puntos, pero solo tiene comas o espacios para evitar la ambigüedad. Esto es perfectamente válido:

```csharp
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

La sintaxis de los datos de ruta de acceso SVG formalmente está documentada en [8.3 de la sección de la especificación SVG](http://www.w3.org/TR/SVG11/paths.html#PathData). Este es un resumen:

## <a name="moveto"></a>**MoveTo**

```csharp
M x y
```

Esto inicia un nuevo contorno en la ruta de acceso estableciendo la posición actual. Datos de ruta de acceso deben comenzar siempre por un `M` comando.

## <a name="lineto"></a>**LineTo**

```csharp
L x y ...
```

Este comando agrega una línea recta (o líneas) a la ruta de acceso y establece la nueva posición actual hasta el final de la última línea. Puede seguir el `L` con varios pares de *x* y *y* coordenadas.

## <a name="horizontal-lineto"></a>**LineTo horizontal**

```csharp
H x ...
```

Este comando agrega una línea horizontal a la ruta de acceso y establece la nueva posición actual hasta el final de la línea. Puede seguir el `H` comando con varios *x* coordenadas, pero no tiene mucho sentido.

## <a name="vertical-line"></a>**Línea vertical**

```csharp
V y ...
```

Este comando agrega una línea vertical a la ruta de acceso y establece la nueva posición actual hasta el final de la línea.

## <a name="close"></a>**Cerrar**

```csharp
Z
```

El `C` comando cierra el contorno agregando una línea recta desde la posición actual hasta el principio de la curva.

## <a name="arcto"></a>**ArcTo**

El comando para agregar un arco elíptico al contorno con diferencia es el comando más complejo en toda la especificación de ruta de acceso de datos SVG. Es el único comando en el que los números pueden representar un valor distinto de valores de coordenadas:

```csharp
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

El *rx* y *red* parámetros son los radios horizontales y verticales de la elipse. El *ángulo de giro* es hacia la derecha en grados.

Establecer el *grandes de arco marca* en 1 para el arco grande o en 0 para el arco pequeño.

Establecer el *barrido marca* en 1 para hacia la derecha y en 0 para hacia la izquierda.

El arco se dibuja en el punto (*x*, *y*), que se convierte en la nueva posición actual.

## <a name="cubicto"></a>**CubicTo**

```csharp
C x1 y1 x2 y2 x3 y3 ...
```

Este comando agrega una curva Bézier cúbica desde la posición actual hasta (*x3*, *y3*), que se convierte en la nueva posición actual. Los puntos (*x1*, *y1*) y (*x2*, *y2*) son puntos de control.

Se pueden especificar varias curvas de Bézier una sola `C` comando. El número de puntos debe ser un múltiplo de 3.

También es un comando de curva de Bézier "smooth":

```csharp
S x2 y2 x3 y3 ...
```

Este comando debe seguir un comando Bézier normal (aunque no sea estrictamente necesario). El comando Bézier smooth calcula el primer punto de control para que sea un reflejo del segundo punto de control de la curva anterior alrededor de su punto de mutua. Lo que estos tres puntos son colineales, y la conexión entre las dos curvas de Bézier es muy sencilla.

## <a name="quadto"></a>**QuadTo**

```csharp
Q x1 y1 x2 y2 ...
```

De las curvas de Bézier cuadráticas, el número de puntos debe ser un múltiplo de 2. Es el punto de control (*x1*, *y1*) y el punto final (y la nueva posición actual) están (*x2*, *y2*)

También es un comando de curva cuadrática suavizada:

```csharp
T x2 y2 ...
```

El punto de control se calcula basándose en el punto de control de la curva cuadrática anterior.

Todos estos comandos también están disponibles en las versiones "relativas", donde los puntos de coordenadas son relativas a la posición actual. Estos comandos relativos empiezan con letras en minúsculas, por ejemplo, `c` en lugar de `C` para la versión relativa del comando Bézier cúbica.

Se trata de la extensión de la definición de datos de la ruta de acceso SVG. Hay una funcionalidad para la repetición de grupos de comandos o para realizar cualquier tipo de cálculo. Comandos de `ConicTo` o los otros tipos de especificaciones de arco no están disponibles.

El método estático [ `SKPath.ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) método espera una cadena válida de comandos SVG. Si se detecta algún error de sintaxis, el método devuelve `null`. Es la indicación de error único.

El [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) método es útil para obtener los datos de ruta de acceso SVG de existente `SKPath` objeto que se va a transferir a otro programa, o almacenar en un formato de archivo basado en texto, como XML. (El `ToSvgPathData` método no se muestra en el código de ejemplo de este artículo.) Hacer *no* espera `ToSvgPathData` para devolver una cadena que corresponde exactamente a las llamadas al método que crea la ruta de acceso. En concreto, descubrirá que arcos se convierten a varios `QuadTo` comandos, y cómo aparecen en los datos de ruta de acceso devuelto desde `ToSvgPathData`.

El **Hello de ruta de acceso de datos** página y espera la palabra "HELLO" con datos de ruta de acceso SVG. Tanto el `SKPath` y `SKPaint` objetos se definen como campos en el [ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) clase:

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

La ruta de acceso a la definición de la cadena de texto comienza en la esquina superior izquierda en el punto (0, 0). Cada letra es de 50 unidades de ancho y alto de 100 unidades y letras se separan mediante otro 25 unidades, lo que significa que la ruta de acceso completa es 350 unidades amplias.

'H' de "Hola" se compone de tres perfiles de una línea, mientras que 'E' es dos curvas de Bézier cúbicas conectadas. Tenga en cuenta que el `C` comando va seguido de seis puntos y dos de los puntos de control tienen coordenadas de -10 y 110, que coloca fuera del intervalo de las coordenadas Y de las demás letras. 'L' está formada por dos líneas conectadas, mientras que el ' o ' es una elipse que se representa con un `A` comando.

Tenga en cuenta que el `M` comando que comienza el contorno última establece la posición en el punto (350, 50), que es el centro vertical de la izquierda lateral de la ' o '. Tal y como se indica a continuación números primero la `A` comando, la elipse tiene un radio horizontal del 25 y un radio vertical de 50. El punto final se indica mediante el último par de números en el `A` comando, que representa el punto (300, 49,9). Que es deliberadamente ligeramente diferente desde el punto inicial. Si el punto de conexión se establece igual que el punto inicial, no se representará el arco. Para dibujar una elipse completa, debe establecer el punto de conexión cerca (pero no es igual a) el punto de inicio, o bien debe usar dos o más `A` comandos, cada uno para la parte de la elipse completa.

Puede agregar la siguiente instrucción al constructor de la página y, a continuación, establezca un punto de interrupción para examinar la cadena resultante:

```csharp
string str = helloPath.ToSvgPathData();
```

Descubrirá que el arco se ha reemplazado con largas series de `Q` comandos para una aproximación por etapas del arco utiliza curvas de Bézier cuadráticas.

El `PaintSurface` controlador obtiene los estrictos límites de la ruta de acceso, que no incluye los puntos de control para 'E' y ' o ' curvas. Las tres transformaciones mover el centro de la ruta de acceso al punto (0, 0), escalar la ruta de acceso para el tamaño del lienzo (pero teniendo en cuenta también el ancho del trazo) y, a continuación, muevan el centro de la ruta de acceso al centro del lienzo:

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

La ruta de acceso rellena el lienzo, que es más razonable cuando se ve en modo horizontal:

[![](path-data-images/pathdatahello-small.png "Captura de pantalla triple de la página de ruta de acceso datos Hello")](path-data-images/pathdatahello-large.png#lightbox "Triple captura de pantalla de la página de ruta de acceso datos Hello")

El **Cat de datos de ruta de acceso** página es similar. Los objetos de ruta de acceso y paint se definen como campos en el [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) clase:

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

El encabezado de un gato es un círculo, y aquí se representa con dos `A` comandos, cada uno de los cuales dibuja un semicírculo. Ambos `A` comandos para el encabezado define los radios horizontal y vertical de 100. El primer arco empieza en (240, 100) y termina en (240, 300), que se convierte en el punto inicial del arco segundo que termina en (240, 100).

Los dos ojos también se representan con dos `A` comandos y al igual que con el encabezado de la cabina, el segundo `A` comando finaliza en el mismo punto que el inicio de la primera `A` comando. Sin embargo, estos pares de `A` comandos no definen una elipse. El con de cada arco es 40 unidades y el radio también es 40 unidades, lo que significa que estos arcos no son semicircles completas.

El `PaintSurface` controlador realiza transformaciones similar que en el ejemplo anterior, pero se establece un valor single `Scale` factor que hay que mantener la relación de aspecto y proporcionar un poco margen para patillas de un de cat no toque los lados de la pantalla:

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

Este es el programa que se ejecuta en las tres plataformas:

[![](path-data-images/pathdatacat-small.png "Captura de pantalla de la página de la ruta de acceso datos Cat triple")](path-data-images/pathdatacat-large.png#lightbox "Triple captura de pantalla de la página de Cat de datos de ruta de acceso")

Normalmente, cuando un `SKPath` objeto se define como un campo, se deben definir los contornos de la ruta de acceso en el constructor u otro método. Sin embargo, cuando se usan datos de ruta de acceso SVG, ha visto que se puede especificar la ruta de acceso completamente en la definición del campo.

El anterior **desagradable reloj analógico** en el [ **gira la transformación** ](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) artículo muestra las manecillas del reloj como líneas simples. El **bastante analógico reloj** siguiente programa reemplaza dichas líneas con `SKPath` objetos que se definan como campos en el [ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) clase junto con `SKPaint` objetos:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

Las manecillas de hora y minuto ahora han delimitado por áreas, por lo que para realizar estas manos distintos entre sí, se dibujan con un contorno negro y relleno gris utilizando la `handStrokePaint` y `handFillPaint` objetos.

En el anterior **desagradable reloj analógico** ejemplo, el poco círculos que marcan las horas y minutos se dibujaban en un bucle. En este **bastante analógico reloj** ejemplo, se usa un enfoque completamente diferente: las marcas de hora y minuto son líneas de puntos que se dibuja con el `minuteMarkPaint` y `hourMarkPaint` objetos:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

El [ **puntos y guiones** ](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) guía describe cómo puede utilizar el `SKPathEffect.CreateDash` método para crear una línea discontinua. El primer argumento es un `float` matriz que normalmente tiene dos elementos: el primer elemento es la longitud de los guiones, y el segundo elemento es la diferencia entre los guiones. Cuando el `StrokeCap` propiedad está establecida en `SKStrokeCap.Round`, a continuación, los extremos redondeados del guión eficazmente alargar la longitud de guión por el ancho del trazo en ambos lados de los guiones. Por lo tanto, si el primer elemento de matriz se establece en 0, crea una línea de puntos.

La distancia entre estos puntos se rige por el segundo elemento de matriz. Como verá en breve, estos dos `SKPaint` objetos se utilizan para dibujar círculos con un radio de 90 unidades. La longitud de esta circunferencia, por tanto, es 180π, lo que significa que las marcas de 60 minutos deben aparecer cada unidades 3π, que es el segundo valor en el `float` la matriz en `minuteMarkPaint`. Las marcas de hora doce deben aparecer cada unidades 15π, que es el valor en el segundo `float` matriz.

El `PrettyAnalogClockPage` clase establece un temporizador para invalidar la superficie de cada 16 milisegundos y la `PaintSurface` con esa tasa se llama al controlador. Las definiciones anteriores de la `SKPath` y `SKPaint` objetos permiten muy limpios código de dibujo:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

Sin embargo algo especial se realiza con la mano de segundo. Dado que el reloj se actualiza cada 16 milisegundos, el `Millisecond` propiedad de la `DateTime` valor potencialmente puede utilizarse para animar un barrido de segundo lado en lugar de uno que se desplaza de saltos discretos de segundo al segundo. Pero este código no permite el movimiento a ser suaves. En su lugar, usa el Xamarin.Forms [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) y [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) animación funciones para otro tipo de movimiento de aceleración. Estas funciones de aceleración provocar la manecilla de los segundos mover de manera es &mdash; recuperar un poco antes de que mueve, y, a continuación, ligeramente over solución su destino, un efecto que lamentablemente no se puede reproducir en estas capturas de pantalla estáticas:

[![](path-data-images/prettyanalogclock-small.png "Captura de pantalla triple de la página de reloj de señales analógicas bastante")](path-data-images/prettyanalogclock-large.png#lightbox "Triple captura de pantalla de la página de reloj de señales analógicas bastante")


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
