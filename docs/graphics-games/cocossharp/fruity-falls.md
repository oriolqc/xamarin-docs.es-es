---
title: Detalles de juego fruity corresponden a las fechas
description: "Esta guía revisa el juego Fruity corresponden a las fechas, tratan conceptos de desarrollo de juegos como física, administración de contenido, estado del juego y diseño de juego y CocosSharp comunes."
ms.topic: article
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 307fdec697f2b94ddfdfe0c380e02fd69e197132
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="fruity-falls-game-details"></a>Detalles de juego fruity corresponden a las fechas

_Esta guía revisa el juego Fruity corresponden a las fechas, tratan conceptos de desarrollo de juegos como física, administración de contenido, estado del juego y diseño de juego y CocosSharp comunes._

Fruity corresponden a las fechas es un juego de basada en física, simple, en el que el Reproductor ordena fruta amarillo y rojo en sectores de almacenamiento coloreadas ganar puntos. El objetivo del juego consiste en conseguir tantos puntos como sea posible sin advertencia una caída de frutas en la Papelera incorrecta, finalizar el juego.

![](fruity-falls-images/image1.png "El objetivo del juego consiste en conseguir tantos puntos como sea posible sin advertencia una caída de frutas en la Papelera incorrecta, finalizar el juego")

Corresponden a las fechas Fruity extiende los conceptos presentados en el [BouncingGame guía](~/graphics-games/cocossharp/first-game/index.md) debe agregar lo siguiente:

 - El contenido de la forma de archivos PNG
 - Física avanzada
 - Estado del juego (transición entre bastidores)
 - Capacidad de optimizar los coeficientes de juegos a través de las variables contenidas en una sola clase
 - Compatibilidad de depuración visual integrada
 - Organización de código mediante las entidades de juegos
 - Juego diseño orientado a la diversión y reproducción de valor

Mientras el BouncingGame se centra en la introducción a los conceptos básicos de CocosSharp, corresponden a las fechas Fruity muestra cómo ponerlo todo en conjunto en un producto acabado de juego. Puesto que esta guía hace referencia a la BouncingGame, los lectores deben primero familiarizarse con la [BouncingGame guía](~/graphics-games/cocossharp/first-game/index.md) antes de leer esta guía.

Esta guía explica la implementación y el diseño de Fruity corresponden a las fechas para proporcionar información detallada para ayudarle a tomar su propio juego. Tratan los siguientes temas:


- [Clase de dispositivo](#GameController_Class)
- [Entidades de juego](#Game_Entities)
- [Gráficos de fruta](#Fruit_Graphics)
- [Física](#Physics)
- [Contenido de juegos](#Game_Content)
- [GameCoefficients](#GameCoefficients)


# <a name="gamecontroller-class"></a>Clase de dispositivo

El proyecto Fruity PCL cae incluye un `GameController` clase que es responsable de crear instancias de la partida y moverse entre bastidores. Esta clase se utiliza con iOS y Android proyectos para eliminar código duplicado.

El código dentro de la `Initialize` método es similar al código en el`Initialize` método en una plantilla de CocosSharp sin cambios, pero contiene un número de modificaciones.

De forma predeterminada, la `GameView.ContentManager.SearchPaths` dependen de la resolución del dispositivo. Como se explica a continuación con más detalle, corresponden a las fechas Fruity usa el mismo contenido independientemente de la resolución del dispositivo, por lo que la `Initialize` método agrega la `Images` ruta de acceso (con sin subcarpetas) a la `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Nuevas plantillas de CocosSharp incluyen una clase que hereda de `CCLayer`, lo que implica que se deben agregar los objetos visuales de juegos y lógica para esta clase. En su lugar, se encuentra Fruity usa varios `CCLayer` instancias al control dibujar orden. Estos `CCLayer` instancias están contenidas en el `GameView` (clase), que se hereda de `CCScene`. Corresponden a las fechas Fruity también incluye varias escenas, el primero es el `TitleScene`. Por lo tanto, la `Initialize` crea una instancia de método un `TitleScene` instancia que se pasa al `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

El contenido de Fruity corresponden a las fechas se creó como carátula de baja resolución píxeles por motivos estéticos. El `GameView.DesignResolution` se establece para que el juego sea solo 384 unidades de ancho y alto 512:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Por último, el `GameController` clase proporciona un método estático que se puede llamar por ninguna `CCGameScene` para realizar la transición a otro `CCScene`. Este método se usa para mover entre la `TitleScene` y `GameScene`.


# <a name="game-entities"></a>Entidades de juego

Corresponden a las fechas Fruity hace uso del patrón de entidad para la mayoría de los objetos del juego. Para obtener una explicación detallada de este patrón se encuentra en la [guían de entidades en CocosSharp](~/graphics-games/cocossharp/entities.md).

Los objetos del juego implementar entidad pueden encontrarse en la carpeta de entidades en el **FruityFalls.Common** proyecto:

![](fruity-falls-images/image2.png "La carpeta de entidades en el proyecto FruityFalls.Common")

Las entidades son objetos que se heredan de `CCNode`y pueden tener objetos visuales, colisión y el comportamiento de cada fotograma.

La `Fruit` objeto representa una entidad CocosSharp típico: contiene un objeto visual, el conflicto y la lógica de cada fotograma. Es responsable de inicializar la fruta su constructor:


```csharp
public Fruit ()
{
    CreateFruitGraphic ();
    if (GameCoefficients.ShowCollisionAreas)
    {
        CreateDebugGraphic ();
    }
    CreateCollision ();
    CreateExtraPointsLabel ();
    Acceleration.Y = GameCoefficients.FruitGravity;
}
```


## <a name="fruit-graphics"></a>Gráficos de fruta

El `CreateFruitGraphic` método crea un `CCSprite` instancia y lo agrega a la `Fruit`. El `IsAntialiased` propiedad se establece en false para dar una apariencia pixelado al juego. Este valor se establece en false en todos los `CCSprite` y `CCLabel` instancias a lo largo del juego:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Cada vez que el Reproductor toca un `Fruit` instancia con la `Paddle`, el valor del punto de ese fruta aumenta en uno. Esto proporciona un desafío adicional para reproductores experimentados hacer juegos malabares con fruta para ganar puntos extra. El valor del punto de la fruta se muestra mediante el `extraPointsLabel` instancia.

El `CreateExtraPointsLabel` método crea un `CCLabel` instancia y lo agrega a la `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Corresponden a las fechas Fruity incluye dos tipos diferentes de fruta: cerezas y limones. El `CreateFruitGraphic` asigna un objeto visual de forma predeterminada, pero los objetos visuales de fruta se pueden cambiar mediante la `FruitColor` propiedad, que a su vez llama `UpdateGraphics`:


```csharp
private void UpdateGraphics()
{
if (GameCoefficients.ShowCollisionAreas)
    {
        debugGrahic.Clear ();
        const float borderWidth = 4;
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius,
            CCColor4B.Black);
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius - borderWidth,
            fruitColor.ToCCColor ());
    }
    if (this.FruitColor == FruitColor.Yellow)
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("lemon.png");
        extraPointsLabel.Color = CCColor3B.Black;
        extraPointsLabel.PositionY = 0;
    }
    else
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("cherry.png");
        extraPointsLabel.Color = CCColor3B.White;
        extraPointsLabel.PositionY = -8;
    }
}
```

La primera instrucción if en `UpdateGraphics` actualiza los gráficos de depuración, que se utilizan para visualizar las áreas de conflicto. Estos objetos visuales se desactivan antes de una versión final del juego se realiza, pero se puede conservar en durante el desarrollo para la depuración física. La segunda pieza cambiará la `graphic.Texture` propiedad mediante una llamada a `CCTextureCache.SharedTextureCache.AddImage`. Este método proporciona acceso a una textura por nombre de archivo. Obtener más información sobre este método puede encontrarse en el [Guía de almacenamiento en caché de textura](~/graphics-games/cocossharp/texture-cache.md).

El `extraPointsLabel` color se ajusta para mantener contraste con la imagen de fruta y su `PositionY` valor se ajusta al centro de la `CCLabel` en las frutas `CCSprite`:

![](fruity-falls-images/image3.png "El color de extraPointsLabel se ajusta para mantener contraste con la imagen de fruta y su valor PositionY se ajusta para centrar CCLabel en las frutas CCSprite")

![](fruity-falls-images/image4.png "El color de extraPointsLabel se ajusta para mantener contraste con la imagen de fruta y su valor PositionY se ajusta para centrar CCLabel en las frutas CCSprite")


## <a name="collision"></a>Colisión

Fruity corresponden a las fechas implementa una solución personalizada colisión con objetos en la carpeta de geometría:

![](fruity-falls-images/image5.png "Fruity corresponden a las fechas implementa una solución personalizada colisión con objetos en la carpeta de geometría")

Colisión de Fruity corresponden a las fechas se implementa mediante el `Circle` o `Polygon` objeto, normalmente con uno de estos dos tipos que se va a agregar como elemento secundario de una entidad. Por ejemplo, el `Fruit` objeto tiene una `Circle` llama `Collision` que inicializa en su `CreateCollision` método:


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Tenga en cuenta que la `Circle` clase hereda de la `CCNode` de la clase, por lo que puede agregarse como un elemento secundario a entidades de nuestro juego:


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` es similar a la creación de `Circle` creación, como se muestra en el `Paddle` clase:


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

Se trata la lógica de colisión [más adelante en esta guía](#Collision).


# <a name="physics"></a>Física

La física en Fruity corresponden a las fechas se puede dividir en dos categorías: movimiento y la colisión. 


## <a name="movement-using-velocity-and-acceleration"></a>Con la velocidad y la aceleración de movimiento

Usa corresponden a las fechas Fruity `Velocity` y `Acceleration` valores para controlar el movimiento de sus entidades, similar a la [BouncingGame](~/graphics-games/cocossharp/first-game/index.md). Entidades implementan su lógica de movimiento de un método denominado `Activity`, que se llama una vez por fotograma. Por ejemplo, podemos ver la implementación del movimiento en el `Fruit` clase `Activity` método:

```csharp
public void Activity(float frameTimeInSeconds)
{
    timeUntilExtraPointsCanBeAdded -= frameTimeInSeconds;
    
    // linear approximation:
    this.Velocity += Acceleration * frameTimeInSeconds;

    // This is a linear approximation to drag. It's used to
    // keep the object from falling too fast, and eventually
    // to slow its horizontal movement. This makes the game easier
    this.Velocity -= Velocity * GameCoefficients.FruitDrag * frameTimeInSeconds;
    
    this.Position += Velocity * frameTimeInSeconds;
}
```
La `Fruit` objeto es único en su implementación de arrastre: un valor que reduce la velocidad en relación con rapidez las frutas es móvil. Esta implementación de arrastre proporciona un *progreso terminal*, que es la velocidad máxima que puede recuperar una instancia de fruta. Arrastre también ralentiza el movimiento horizontal de fruta, lo que facilita el juego ligeramente reproducir.

El `Paddle` también se aplica el objeto `Velocity` en su `Activity` método, pero su `Velocity` se calcula utilizando una `desiredLocation` valor:


```csharp
public void Activity(float frameTimeInSeconds)
{
    // This code will cause the cursor to lag behind the touch point
    // Increasing this value reduces how far the paddle lags
    // behind the player’s finger. 
    const float velocityCoefficient = 4;
    // Get the velocity from current location and touch location
    Velocity = (desiredLocation - this.Position) * velocityCoefficient;
    this.Position += Velocity * frameTimeInSeconds;
    ...
}
```

Normalmente, los juegos que usan `Velocity` controlar la posición de los objetos no no directamente establece posiciones de la entidad, al menos no después de la inicialización. En lugar de establecer directamente la `Paddle` posición, el `Paddle.HandleInput` método asigna el `desiredLocation` valor:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

## <a name="collision"></a>Colisión

Corresponden a las fechas Fruity implementa parcial realista colisión entre las frutas y otros objetos collidable como el `Paddle` y `GameScene.Splitter`. Para facilitar la depuración de colisión, áreas de colisión de Fruity corresponden a las fechas pueden hacerse visibles cambiando el `GameCoefficients.ShowDebugInfo` en el `GameCoefficients.cs` archivo:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Establecer este valor en `true` permite la representación de las áreas de conflicto:  

![](fruity-falls-images/image6.png "Este valor en true habilita la representación de áreas de colisión")

Comienza la lógica de colisión en la `GameScene.Activity` método:


```csharp
private void Activity(float frameTimeInSeconds)
{
    if (hasGameEnded == false)
    {
        paddle.Activity(frameTimeInSeconds);
        foreach (var fruit in fruitList)
        {
            fruit.Activity(frameTimeInSeconds);
        }
        spawner.Activity(frameTimeInSeconds);
        DebugActivity();
        PerformCollision();
    }
}
```

`PerformCollision` controla entren todos `Fruit` instancias con otros objetos: 


```csharp
private void PerformCollision()
{
    // reverse for loop since fruit may be destroyed:
    for(int i = fruitList.Count - 1; i > -1; i--)
    {
        var fruit = fruitList[i];
        FruitVsPaddle(fruit);
        FruitPolygonCollision(fruit, splitter.Polygon, CCPoint.Zero);
        FruitVsBorders(fruit);
        FruitVsBins(fruit);
    }
}
```

### <a name="fruitvsborders"></a>FruitVsBorders
`FruitVsBorders` colisión realiza su propia lógica de colisión en lugar de confiar en la lógica contenidas en una clase diferente. Esta diferencia existe porque la colisión entre fruta y bordes de la pantalla no es absolutamente sólida – es posible que fruta en movimiento paleta cuidado insertarse fuera del borde de la pantalla. Fruta se animará fuera de la pantalla cuando se llama con la paleta, pero si el Reproductor inserta lenta fruta se moverá más allá del borde y fuera de la pantalla:


```csharp
private void FruitVsBorders(Fruit fruit)
{
    if(fruit.PositionX - fruit.Radius < 0 && fruit.Velocity.X < 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionX + fruit.Radius > gameplayLayer.ContentSize.Width && fruit.Velocity.X > 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionY + fruit.Radius > gameplayLayer.ContentSize.Height && fruit.Velocity.Y > 0)
    {
        fruit.Velocity.Y *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
}
```

### <a name="fruitvsbins"></a>FruitVsBins
El `FruitVsBins` método es responsable de comprobar si se ha retrasado cualquier fruta en una de las dos ubicaciones. Si es así, el Reproductor se le adjudica puntos (si la Papelera de fruta colores a coincidencia) o la partida finaliza (si los colores no coinciden):


```csharp
private void FruitVsBins(Fruit fruit)
{
    foreach(var bin in fruitBins)
    {
        if(bin.Polygon.CollideAgainst(fruit.Collision))
        {
            if(bin.FruitColor == fruit.FruitColor)
            {
                // award points:
                score += 1 + fruit.ExtraPointValue;
                scoreText.Score = score;
                Destroy(fruit);
            }
            else
            {
                EndGame();
            }
            break;
        }
    }
}
```

### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle y FruitPolygonCollision
Fruta frente a la paleta y fruta frente a divisor (el área de separar las dos ubicaciones) colisión ambos confían en el `FruitPolygonCollision` método. Este método tiene tres partes:

1. Comprobar si los objetos estén en conflicto
1. Mover los objetos (solo las frutas en Fruity corresponden a las fechas), por lo que ya no se superpongan
1. Ajustar la velocidad de los objetos (solo las frutas en Fruity cae) para simular un rebote que el código siguiente muestra los puntos mencionados anteriormente:


```csharp
private static bool FruitPolygonCollision(Fruit fruit, Polygon polygon, CCPoint polygonVelocity)
{
    // Test whether the fruit collides
    bool didCollide = polygon.CollideAgainst(fruit.Collision);
    if (didCollide)
    {
        var circle = fruit.Collision;
        // Get the separation vector to reposition the fruit so it doesn't overlap the polygon
        var separation = CollisionResponse.GetSeparationVector(circle, polygon);
        fruit.Position += separation;
        // Adjust the fruit's Velocity to make it bounce:
        var normal = separation;
        normal.Normalize();
        fruit.Velocity = CollisionResponse.ApplyBounce(
            fruit.Velocity, 
            polygonVelocity, 
            normal, 
            GameCoefficients.FruitCollisionElasticity);
    }
    return didCollide;
}
```

Respuesta de colisión de Fruity corresponden a las fechas es medias: solo las frutas progreso y la posición se ajustan. Merece la pena mencionar que otros juegos pueden tener colisión que afecta a los objetos implicados, como un carácter de insertar un boulder o dos coches bloqueado en entre sí.

La expresión matemática detrás de la lógica de colisión contenida en el `Polygon` y `CollisionResponse` clases queda fuera del ámbito de esta guía, pero como escrito, estos métodos se pueden usar para muchos tipos de juegos. El polígono y `CollisionResponse` clases admiten incluso polígonos convexas y no rectangulares, por lo que este código se puede usar para muchos tipos de juegos.

 


# <a name="game-content"></a>Contenido de juegos

El material gráfico en Fruity cae distingue inmediatamente el juego de la BouncingGame. Aunque los diseños de juegos son similares, reproductores verá inmediatamente una diferencia en el aspecto de los juegos de dos. A menudo jugadores deciden si desea probar un juego por los objetos visuales. Por lo tanto, es sumamente importante que los desarrolladores invierten recursos para realizar un visualmente atractivo juegos.

La carátula de Fruity corresponden a las fechas se creó con los siguientes objetivos:

 - Tema coherente
 - Énfasis en un solo carácter: el mono Xamarin
 - Colores brillantes para crear un relaja divertida experiencia
 - Contraste entre el primer plano y fondo para que objetos de juego son fáciles de controlar visualmente
 - Capacidad para crear efectos visuales simples sin animaciones de uso intensivo de recursos


## <a name="content-location"></a>Ubicación del contenido

Corresponden a las fechas Fruity incluye todo su contenido en la carpeta imágenes en el proyecto de Android:

![](fruity-falls-images/image7.png "Corresponden a las fechas Fruity incluye todo su contenido en la carpeta imágenes en el proyecto de Android")

Estos mismos archivos se vinculan en el proyecto de iOS para evitar la duplicación, y así cambios en los archivos afectan ambos proyectos:

![](fruity-falls-images/image8.png "Estos mismos archivos se vinculan en el proyecto de iOS para evitar la duplicación, y así cambios en los archivos afectan ambos proyectos")

Merece la pena indicar que el contenido no está contenido dentro de la **Ld** o **Hd** carpetas, que forman parte de la plantilla de CocosSharp predeterminada. El **Ld** y **Hd** carpetas están diseñadas para usarse para juegos que proporcionan dos conjuntos de contenido: uno para dispositivos de menor resolución, como teléfonos y uno para dispositivos de mayor resolución, como tabletas. El material gráfico Fruity corresponden a las fechas se intencionadamente crea con un pixelado estético, por lo que no es necesario proporcionar contenido de diferentes tamaños de pantalla. Por lo tanto, la **Ld** y **Hd** carpetas se han quitado completamente desde el proyecto.


## <a name="gamescene-layering"></a>GameScene capas

Como se mencionó anteriormente en esta guía, el GameScene es responsable de todas las creación de instancias de objetos del juego, colocar y lógica entre objeto (por ejemplo, colisión). Todos los objetos se agregan a uno de los cuatro `CCLayer` instancias:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Estas cuatro capas se crean en el `CreateLayers` método. Tenga en cuenta que se agregan a la `GameScene` en orden de atrás a la parte delantera:


```csharp
private void CreateLayers()
{
    backgroundLayer = new CCLayer();
    this.AddLayer(backgroundLayer);
    gameplayLayer = new CCLayer();
    this.AddLayer(gameplayLayer);
    foregroundLayer = new CCLayer();
    this.AddLayer(foregroundLayer);
    hudLayer = new CCLayer();
    this.AddLayer(hudLayer);
}
```

Una vez creadas las capas, se agregan todos los objetos visuales a las capas utilizando la `AddChild` método, como se muestra en el `CreateBackground` método:


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


## <a name="vine-entity"></a>Entidad de vid

El `Vine` entidad se utiliza exclusivamente para el contenido: no tiene efecto en el juego. Se compone de 20 `CCSprite` instancias, un número seleccionado por prueba y error para asegurarse de que la vid siempre alcanza la parte superior de la pantalla:


```csharp
public Vine ()
{
    const int numberOfVinesToAdd = 20;
    for (int i = 0; i < numberOfVinesToAdd; i++)
    {
        var graphic = new CCSprite ("vine.png");
        graphic.AnchorPoint = new CCPoint(.5f, 0);
        graphic.PositionY = i * graphic.ContentSize.Height;
        this.AddChild (graphic);
    }
}
```

La primera `CCSprite` se sitúa por lo que su borde inferior coincide con la posición de la vid. Esto se consigue estableciendo la AnchorPoint en `new CCPoint(.5f, 0)`. El `AnchorPoint` usos de propiedad *normalizado coordenadas* el intervalo entre 0 y 1, independientemente del tamaño de la `CCSprite`:

![](fruity-falls-images/image9.png "La propiedad AnchorPoint usa coordenadas normalizados el intervalo entre 0 y 1, independientemente del tamaño de la CCSprite")

Sprites VID subsiguientes se colocan utilizando el `graphic.ContentSize.Height` valor, que devuelve el alto de la imagen en píxeles. El diagrama siguiente muestra cómo el gráfico de vid iconos hacia arriba:

![](fruity-falls-images/image10.png "Este diagrama muestra cómo el gráfico de vid iconos hacia arriba")

Desde el origen de la `Vine` entidad está en la parte inferior de la vid, a continuación, colóquela es sencillo, como se muestra en el `Paddle.CreateVines` método:


```csharp
private void CreateVines()
{
    // Increasing this value moves the vines closer to the 
    // center of the paddle.
    const int vineDistanceFromEdge = 4;
    leftVine = new Vine ();
    var leftEdge = -width / 2.0f;
    leftVine.PositionX = leftEdge + vineDistanceFromEdge;
    this.AddChild (leftVine);
    rightVine = new Vine ();
    var rightEdge = width / 2.0f;
    rightVine.PositionX = rightEdge - vineDistanceFromEdge;
    this.AddChild (rightVine);
}
```

La vid instancias en el `Paddle` entidad también tienen un comportamiento de rotación interesantes. Puesto que la `Paddle` entidad como un todo gira según los datos proporcionados por el Reproductor (que esta guía se describe con más detalle a continuación), el `Vine` instancias también se ve afectadas por este giro. Sin embargo, la rotación del `Vine` instancias junto con el `Paddle` produce un efecto visual no deseado como se muestra en la siguiente animación:

![](fruity-falls-images/image11.gif "Sin embargo, la rotación de las instancias de vid junto con la paleta produce un efecto visual no deseado como se muestra en la siguiente animación")

Para contrarrestar el `Paddle` rotación, el `leftVine` y `rightVine` rotan instancias la dirección opuesta de la paleta, tal y como se muestra en el `Paddle.Activity` método:


```csharp
public void Activity(float frameTimeInSeconds)
{
    ...
    // This value adds a slight amount of rotation
    // to the vine. Having a small amount of tilt looks nice.
    float vineAngle = this.Velocity.X / 100.0f;
    leftVine.Rotation = -this.Rotation + vineAngle;
    rightVine.Rotation = -this.Rotation + vineAngle;
}
```

Tenga en cuenta que una pequeña cantidad de rotación se agrega a la vid a través de la `vineAngle` coeficiente. Este valor puede cambiarse para ajustar cuánto girar el vines.


# <a name="gamecoefficients"></a>GameCoefficients

Todos los juegos buena es el producto de iteración, por lo que corresponden a las fechas Fruity incluye una clase denominada `GameCoefficients` que controla cómo se reproduce el juego. Esta clase contiene expresivas variables que se usan en el juego para control física, diseño, creación y la puntuación.

Por ejemplo, la física de fruta se controla mediante las siguientes variables:


```csharp
public static class GameCoefficients
{
    ...
    
    // The strength of the gravity. Making this a 
    // smaller (bigger negative) number will make the
    // fruit fall faster. A larger (smaller negative) number
    // will make the fruit more floaty.
    public const float FruitGravity = -60;
    // The impact of "air drag" on the fruit, which gives
    // the fruit terminal velocity (max falling speed) and slows
    // the fruit's horizontal movement (makes the game easier)
    public const float FruitDrag = .1f;
    // Controls fruit collision bouncyness. A value of 1
    // means no momentum is lost. A value of 0 means all
    // momentum is lost. Values greater than 1 create a spring-like effect
    public const float FruitCollisionElasticity = .5f;
    
    ...
}
```

Como se desprende de los nombres de estas variables pueden usarse para ajustar fruta rapidez con que corresponden a las fechas, cómo horizontal movimiento ralentiza con el tiempo y rebote de la paleta.

Juegos coeficientes que se almacenan en archivos de código o datos (por ejemplo, XML) pueden ser especialmente valiosas para los equipos con los diseñadores de juegos que no son programadores.

La `GameCoefficients` clase también incluye valores para activar la información de depuración como la creación de información o colisión áreas:


```csharp
public static class GameCoefficients
{
    ...
    // This controls whether debug information is displayed on screen.
    public const bool ShowDebugInfo = false;
    public const bool ShowCollisionAreas = false;
    ...
}
```


# <a name="conclusion"></a>Conclusión

Esta guía se han explorado el juego Fruity corresponden a las fechas. Tratan conceptos como contenido, física y administración de estado del juego.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Proyecto completado (ejemplo)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
