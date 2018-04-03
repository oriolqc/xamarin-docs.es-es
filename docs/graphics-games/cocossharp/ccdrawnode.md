---
title: Geometría de dibujo con CCDrawNode
description: CCDrawNode proporciona métodos para dibujar objetos simples como líneas, círculos y triángulos.
ms.topic: article
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 5a2471981f2e88ff8af9a803ff8f5a99e5b9266f
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="drawing-geometry-with-ccdrawnode"></a>Geometría de dibujo con CCDrawNode

_`CCDrawNode` Proporciona métodos para dibujar objetos simples como líneas, círculos y triángulos._

La `CCDrawNode` clase CocosSharp proporciona varios métodos para dibujar formas geométricas comunes. Hereda de la `CCNode` clase y normalmente se agrega a `CCLayer` instancias. Esta guía describe cómo usar `CCDrawNode` instancias para realizar la representación personalizada. También proporciona una lista completa de las funciones de dibujo disponibles con capturas de pantalla y ejemplos de código.


## <a name="creating-a-ccdrawnode"></a>Crear un CCDrawNode

La `CCDrawNode` clase puede usarse para dibujar objetos geométricos como líneas, círculos y rectángulos. Por ejemplo, el ejemplo de código siguiente muestra cómo crear un `CCDrawNode` instancia que dibuja un círculo en un `CCLayer` implementar clase:


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

Este código genera el siguiente círculo en tiempo de ejecución:

![](ccdrawnode-images/image1.png "Este código genera esta círculo en tiempo de ejecución")


## <a name="draw-method-details"></a>Dibujar los detalles de método

¡Eche un vistazo a algunos detalles relacionados con el dibujo con un `CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>Dibujar métodos posiciones son con respecto a la CCDrawNode

Todos los métodos de dibujo necesitan valor de al menos una posición para dibujar. Este valor de posición es relativa a la `CCDrawNode` instancia. Esto significa que la `CCDrawNode` él mismo tiene una posición y todos dibujar las llamadas realizadas en el `CCDrawNode` tener uno o más valores de posición. Para ayudar a entender cómo se combinan estos valores, veamos algunos ejemplos.

En primer lugar se examinará el `DrawCircle` ejemplo anterior:


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

En este caso, el `CCDrawNode` se coloca en (100,100), y es el círculo dibujado en (0,0) relativa a la `CCDrawNode`, resultante en el círculo está centrados 100 píxeles hacia arriba y hacia la derecha de la esquina inferior izquierda de la pantalla del juego.

El `CCDrawNode` también se pueden colocar en el origen (parte inferior izquierda de la pantalla), confiando en el círculo de desplazamientos:


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

El código anterior, el resultado en el centro del círculo de 50 unidades (`drawNode.PositionX` + la `CCPoint.X`) a la derecha del lado izquierdo de la pantalla y 60 (`drawNode.PositionY` + la `CCPoint.Y`) unidades por encima de la parte inferior de la pantalla.

Una vez que se ha llamado a un método de dibujo, no se puede modificar el objeto dibujado a menos que la `CCDrawNode.Clear` se llama al método, por lo que cualquier cambio de posición debe llevar a cabo en el `CCDrawNode` propio.

Objetos dibujados por `CCNodes` también se ve afectada por la `CCNode` la instancia `Rotation` y `Scale` propiedades.


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>No es necesario que los métodos de dibujo llamará cada marco

Dibuje la necesidad de métodos que se llamará solo una vez para crear un objeto visual persistente. En el ejemplo anterior, la llamada a `DrawCircle` en el constructor de la `GameLayer` : `DrawCircle` no debe llamarse cada marco para volver a dibujar el círculo cuando se actualiza la pantalla.

Esto difiere de los métodos de dibujo en MonoGame, que normalmente representarán algo en la pantalla para un solo fotograma, y que debe llamarse cada fotograma.

Si se llaman a métodos de dibujar cada fotograma, objetos finalmente se acumularán dentro de la llamada a `CCDrawNode` instancia, lo que produce una disminución en la velocidad de fotogramas tal y como se dibujan varios objetos.


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>Cada CCDrawNode admite varias llamadas de dibujo

`CCDrawNode` instancias se pueden utilizar para dibujar varias formas. Esto permite que se incluye en un único objeto complejos objetos visuales. Por ejemplo, el código siguiente se puede usar para presentar varios círculos con una `CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

El resultado es el siguiente gráfico:

![](ccdrawnode-images/image2.png "Esto da como resultado en el gráfico siguiente")


## <a name="draw-call-examples"></a>Ejemplos de llamadas a Draw

Están disponibles en las siguientes llamadas a draw `CCDrawNode`:

- [`DrawCatmullRom`](#drawcatmullrom)
- [`DrawCircle`](#drawcircle)
- [`DrawCubicBezier`](#drawcubicbezier)
- [`DrawEllipse`](#drawellipse)
- [`DrawLineList`](#drawlinelist)
- [`DrawPolygon`](#drawpolygon)
- [`DrawQuadBezier`](#drawquadbezier)
- [`DrawRect`](#drawrect)
- [`DrawSegment`](#drawsegment)
- [`DrawSolidArc`](#drawsolidarc)
- [`DrawSolidCircle`](#drawsolidcircle)
- [`DrawTriangleList`](#drawtrianglelist)


### <a name="drawcardinalspline"></a>DrawCardinalSpline

`DrawCardinalSpline` crea una línea curva a través de un número variable de puntos. 

El `config` parámetro define qué puntos de la curva spline pasará a través. El ejemplo siguiente muestra una curva spline que pasa a través de cuatro puntos.

El `tension` parámetro controla cómo nitidez o los puntos de la curva spline de ida y vuelta aparecen. A `tension` dará como resultado el valor 0 en una curva spline y un `tension` dará como resultado el valor 1 en una curva spline dibujada por líneas rectas y bordes de disco duros.

Aunque b-spline es líneas curvas, CocosSharp dibuja b-spline con líneas rectas. El `segments` parámetro controla cuántos segmentos para usar para dibujar la curva spline. Un número mayor da como resultado una suavemente curva spline de Bézier a costa del rendimiento. 

Ejemplo de código:


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "El parámetro de segmentos controla cuántos segmentos para usar para dibujar la curva spline")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` crea una línea curva a través de un número variable de puntos, similar a `DrawCardinalLine`. Este método no incluye un parámetro de tensión.

Ejemplo de código:

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom crea una línea curva a través de un número variable de puntos, parecidos a DrawCardinalLine")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` crea un perímetro de un círculo de un determinado `radius`.

Ejemplo de código:

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle crea un perímetro de un círculo de un radio especificado")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` Dibuja una línea curva entre dos puntos, con puntos de control para establecer la ruta de acceso entre los dos puntos.

Ejemplo de código:

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier dibuja una línea curva entre dos puntos.")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` crea el esquema de un *elipse*, que se conoce a menudo como una elipse (aunque ambos no son idénticos geométrica). La forma de la elipse puede definirse mediante un `CCRect` instancia.

Ejemplo de código:


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse crea el esquema de una elipse, que a menudo se conoce como una elipse")


### <a name="drawline"></a>DrawLine

`DrawLine` se conecta a los puntos con una línea de un determinado valor de ancho. Este método es similar a `DrawSegment`, excepto en que crea puntos de conexión sin formato en lugar de puntos de conexión de redondeo.

Ejemplo de código:


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine se conecta a los puntos con una línea de un determinado valor de ancho")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` crea varias líneas mediante la conexión de cada par de puntos especificados por un `CCV3F_C4B` matriz. El `CCV3F_C4B` struct contiene valores de posición y el color. El `verts` parámetro siempre debe contener un número par de puntos, como cada línea se define mediante dos puntos.

Ejemplo de código:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "El parámetro verts siempre debe contener un número par de puntos, como cada línea se define mediante dos puntos")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` crea un polígono rellenada con un contorno de color y de ancho variable.

Ejemplo de código:


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon crea un polígono rellenada con un contorno de color y de ancho variable")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` se conecta a dos puntos con una línea. Se comporta del mismo modo que `DrawCubicBezier` pero solo admite un único punto de control.

Ejemplo de código:


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier conecta dos puntos con una línea")


### <a name="drawrect"></a>DrawRect

`DrawRect` crea un rectángulo rellenada con un contorno de color y de ancho variable.

Ejemplo de código: 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect crea un rectángulo rellenada con un contorno de color y de ancho variable")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` se conecta a dos puntos con una línea de ancho variable y el color. Es similar a `DrawLine`, excepto en que crea extremos redondeados en lugar de los puntos de conexión sin formato.

Ejemplo de código:


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment conecta dos puntos con una línea de ancho variable y color")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` crea una cuña rellenado de un color determinado y radius.

Ejemplo de código:


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc crea una cuña rellenado de un color determinado y radio")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` crea un círculo rellenado de un radio especificado.

Ejemplo de código:


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle da lugar a un círculo rellenado de un radio especificado")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` crea una lista de triángulos. Cada triángulo se define mediante tres `CCV3F_C4B` instancias en una matriz. El número de vértices en la matriz se pasa a la `verts` parámetro debe ser un múltiplo de tres. Tenga en cuenta que la única información contenida en `CCV3F_C4B` es la posición de la verts y su color: el `DrawTriangleList` método no es compatible con el dibujo triángulos con texturas.

Ejemplo de código:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList crea una lista de triángulos")


## <a name="summary"></a>Resumen

Esta guía explica cómo crear un `CCDrawNode` y realizar el procesamiento basado en el tipo primitivo. Proporciona un ejemplo de cada una de las llamadas a draw.

## <a name="related-links"></a>Vínculos relacionados

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [Ejemplo completo](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
