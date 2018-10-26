---
title: Dibujar geometría con CCDrawNode
description: Este documento describe CCDrawNode, que proporciona métodos para dibujar objetos simples como líneas, círculos y triángulos.
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: b910e136366c429de8bd2ba1ac959882b4d7201d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123198"
---
# <a name="drawing-geometry-with-ccdrawnode"></a>Dibujar geometría con CCDrawNode

_`CCDrawNode` Proporciona métodos para dibujar objetos simples como líneas, círculos y triángulos._

La `CCDrawNode` clase CocosSharp proporciona varios métodos para dibujar formas geométricas comunes. Hereda de la `CCNode` clase y normalmente se agrega a `CCLayer` instancias. Esta guía explica cómo usar `CCDrawNode` instancias para realizar el procesamiento personalizado. También se proporciona una lista completa de las funciones de dibujo disponibles con capturas de pantalla y ejemplos de código.


## <a name="creating-a-ccdrawnode"></a>Creación de un CCDrawNode

La `CCDrawNode` clase puede utilizarse para dibujar objetos geométricos, como líneas, rectángulos y círculos. Por ejemplo, el ejemplo de código siguiente muestra cómo crear un `CCDrawNode` instancia que dibuja un círculo en un `CCLayer` implementación de clase:


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

Este código genera el círculo en tiempo de ejecución siguiente:

![](ccdrawnode-images/image1.png "Este código genera este círculo en tiempo de ejecución")


## <a name="draw-method-details"></a>Detalles del método Draw

Echemos un vistazo a algunos detalles relacionados con el dibujo con un `CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>Las posiciones de los métodos Draw son en relación con el CCDrawNode

Todos los métodos draw requieren el valor de al menos una posición para el dibujo. Este valor de posición es relativa a la `CCDrawNode` instancia. Esto significa que el `CCDrawNode` sí tiene una posición y todas las llamadas realizadas en dibujar el `CCDrawNode` también aprovechar uno o más valores de posición. Para ayudarle a entender cómo combinan estos valores, echemos un vistazo a algunos ejemplos.

Primero analizaremos la `DrawCircle` ejemplo anterior:


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

En este caso, el `CCDrawNode` se coloca en (100,100), y es el círculo dibujado en (0,0) relativa a la `CCDrawNode`, lo que en el círculo que se va a centrados 100 píxeles hacia arriba y hacia la derecha de la esquina inferior izquierda de la pantalla de juego.

El `CCDrawNode` también se pueden colocar en el origen (parte inferior izquierda de la pantalla), dependen de la circunferencia de desplazamientos:


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

El código anterior, el resultado en el centro del círculo de 50 unidades (`drawNode.PositionX` + el `CCPoint.X`) a la derecha de la parte izquierda de la pantalla y 60 (`drawNode.PositionY` + el `CCPoint.Y`) unidades por encima de la parte inferior de la pantalla.

Una vez que se ha llamado a un método draw, no se puede modificar el objeto dibujado a menos que el `CCDrawNode.Clear` se llama al método, por lo que cualquier cambio de posición debe realizarse en el `CCDrawNode` propio.

Objetos dibujados por `CCNodes` también se ven afectadas por la `CCNode` la instancia `Rotation` y `Scale` propiedades.


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>No es necesario que los métodos Draw se llama a cada fotograma

Los métodos Draw deben llamarse una sola vez para crear un objeto visual persistente. En el ejemplo anterior, la llamada a `DrawCircle` en el constructor de la `GameLayer` – `DrawCircle` no deben llamarse cada fotograma para volver a dibujar el círculo cuando se actualiza la pantalla.

Esto difiere de los métodos de dibujo de MonoGame, que normalmente representarán algo en la pantalla para un solo fotograma, y que se debe llamar a cada fotograma.

Si se llaman a métodos de dibujo cada fotograma, finalmente se acumularán los objetos dentro de la llamada a `CCDrawNode` instancia, lo que produce una disminución en la velocidad de fotogramas tal como se dibujan más objetos.


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>Cada CCDrawNode admite varias llamadas de dibujo

`CCDrawNode` las instancias pueden utilizarse para dibujar varias formas. Esto permite que los objetos visuales complejos que se incluye en un único objeto. Por ejemplo, el código siguiente puede utilizarse para representar varios círculos con uno `CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

Esto da como resultado el siguiente gráfico:

![](ccdrawnode-images/image2.png "Esto da como resultado de este gráfico")


## <a name="draw-call-examples"></a>Ejemplos de llamadas a Draw

Las siguientes llamadas de dibujo están disponibles en `CCDrawNode`:

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

El `config` parámetro define qué puntos de la curva polinomial pasará directamente. El ejemplo siguiente muestra una curva spline que pasa a través de cuatro puntos.

El `tension` parámetro controla cómo nítida o los puntos de la curva polinomial de ida y vuelta aparecen. Un `tension` dará como resultado el valor 0 en una curva spline y un `tension` dará como resultado el valor 1 en una curva spline dibujada líneas rectas y bordes de disco duros.

Aunque splines son líneas curvas, CocosSharp dibuja curvas spline con líneas rectas. El `segments` parámetro controla cuántos segmentos para utilizar para dibujar la curva polinomial. Un número mayor da como resultado una suavemente curva spline a costa del rendimiento. 

Ejemplo de código:


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "El parámetro de segmentos controla cuántos segmentos para utilizar para dibujar la curva polinomial")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` crea una línea curva a través de un número variable de puntos, similar a `DrawCardinalLine`. Este método no incluye un parámetro de tensión.

Ejemplo de código:

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom crea una línea curva a través de un número variable de puntos, similares a DrawCardinalLine")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` crea un perímetro de un círculo de un determinado `radius`.

Ejemplo de código:

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle crea un perímetro de un círculo de un radio determinado")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` Dibuja una línea curva entre dos puntos, con puntos de control para establecer la ruta de acceso entre los dos puntos.

Ejemplo de código:

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier dibuja una línea curva entre dos puntos")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` crea el esquema de un *elipse*, que se conoce a menudo como una elipse (aunque las dos no son geométricamente idénticas). La forma de la elipse puede definirse mediante una `CCRect` instancia.

Ejemplo de código:


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse crea el esquema de una elipse, que a menudo se conoce como una elipse")


### <a name="drawline"></a>DrawLine

`DrawLine` se conecta a los puntos de una línea de un ancho especificado. Este método es similar a `DrawSegment`, excepto en que crea puntos de conexión sin formato en lugar de puntos de conexión de redondeo.

Ejemplo de código:


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine se conecta a los puntos de una línea de un ancho especificado")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` crea varias líneas mediante la conexión de cada par de puntos especificado por un `CCV3F_C4B` matriz. El `CCV3F_C4B` struct contiene los valores de posición y color. El `verts` parámetro siempre debe contener un número par de puntos, y cada línea se define mediante dos puntos.

Ejemplo de código:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "El parámetro verts siempre debe contener un número par de puntos, como cada línea se define mediante dos puntos")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` crea un polígono de relleno con un esquema de color y de ancho variable.

Ejemplo de código:


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon crea un polígono de relleno con un esquema de color y de ancho variable")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` conecta dos puntos con una línea. Se comporta del mismo modo que `DrawCubicBezier` pero solo admite un único punto de control.

Ejemplo de código:


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier conecta dos puntos con una línea")


### <a name="drawrect"></a>DrawRect

`DrawRect` crea un rectángulo rellenada con un esquema de color y de ancho variable.

Ejemplo de código: 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect crea un rectángulo relleno con un contorno de color y ancho variable")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` conecta dos puntos con una línea de color y ancho variable. Es similar a `DrawLine`, excepto en que crea puntos de conexión de redondeo, en lugar de los puntos de conexión sin formato.

Ejemplo de código:


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment conecta dos puntos con una línea de color y ancho variable")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` crea una cuña rellenado de un determinado color y radius.

Ejemplo de código:


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc crea una cuña rellenado de un determinado color y radius")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` crea un círculo de rellenado de un radio determinado.

Ejemplo de código:


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle crea un círculo relleno en un radio determinado de")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` crea una lista de triángulos. Cada triángulo definido por tres `CCV3F_C4B` instancias en una matriz. El número de vértices de la matriz se pasa a la `verts` parámetro debe ser un múltiplo de tres. Tenga en cuenta que la única información contenida en `CCV3F_C4B` es la posición de la verts y su color – el `DrawTriangleList` método no admite el dibujo de triángulos con texturas.

Ejemplo de código:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList crea una lista de triángulos")


## <a name="summary"></a>Resumen

Esta guía explica cómo crear un `CCDrawNode` y realizar la representación basada en el tipo primitivo. Proporciona un ejemplo de cada una de las llamadas a draw.

## <a name="related-links"></a>Vínculos relacionados

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [Ejemplo completo](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
