---
title: Detalles del juego Fruity corresponden a las fechas
description: Esta guía repasan el juego Fruity caídas, que abarcan CocosSharp comunes y conceptos de desarrollo de juegos, como diseño de juegos, administración de contenido, estado del juego y física.
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 959f5eb149ad375d686b17a85eb3d3b8fbdf3659
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114254"
---
# <a name="fruity-falls-game-details"></a>Detalles del juego Fruity corresponden a las fechas

_Esta guía repasan el juego Fruity caídas, que abarcan CocosSharp comunes y conceptos de desarrollo de juegos, como diseño de juegos, administración de contenido, estado del juego y física._

Caídas Fruity es un juego sencillo, basado en física en el que el Reproductor ordena fruta rojo y amarillo en depósitos coloreados ganar puntos. El objetivo del juego es ganar puntos tantos como sea posible sin permitir que un descenso de frutas en la Papelera incorrecta, finalización del juego.

![](fruity-falls-images/image1.png "El objetivo del juego consiste en conseguir puntos tantos como sea posible sin permitir que un descenso de frutas en la Papelera incorrecta, finalización del juego")

Caídas Fruity extiende los conceptos presentados en la [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md) agregando lo siguiente:

 - El contenido de la forma de imágenes PNG
 - Física avanzada
 - Estado del juego (transición entre escenas)
 - Capacidad de optimizar los coeficientes de juego a través de las variables contenidas en una sola clase
 - Compatibilidad con depuración visual
 - Organización del código mediante entidades de juegos
 - Diseño centrado en el valor de diversión y reproducción de juegos

Mientras el [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md) centrado en la introducción de conceptos clave CocosSharp, Fruity corresponden a las fechas se muestra cómo combínelo todo en un producto acabado de juego. Puesto que esta guía hace referencia a BouncingGame, los lectores deben primero familiarizarse con la [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md) antes de leer esta guía.

Esta guía explica la implementación y diseño de caídas Fruity para proporcionar información detallada para ayudarle a crear su propio juego. Se tratan los temas siguientes:


- [Clase de dispositivo](#gamecontroller-class)
- [Entidades de juegos](#game-entities)
- [Gráficos de frutas](#fruit-graphics)
- [Física](#physics)
- [Contenido del juego](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>Clase de dispositivo

El proyecto PCL cae Fruity incluye un `GameController` clase que es responsable de crear instancias del juego y mover entre bastidores. Esta clase está usando tanto los proyectos de iOS y Android para eliminar código duplicado.

El código dentro de la `Initialize` método es similar al código en el`Initialize` método en una plantilla de CocosSharp sin cambios, pero contiene un número de modificaciones.

De forma predeterminada, el `GameView.ContentManager.SearchPaths` dependen de la resolución del dispositivo. Como se explica más adelante con más detalle, caídas Fruity usa el mismo contenido independientemente de la resolución del dispositivo, por lo que la `Initialize` método agrega el `Images` ruta de acceso (con ninguna subcarpeta) a la `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Nuevas plantillas de CocosSharp incluyen una clase que hereda de `CCLayer`, lo que implica que los objetos visuales de juegos y la lógica deben agregarse a esta clase. En su lugar, se encuentra Fruity usa varios `CCLayer` instancias al control de dibujar el pedido. Estos `CCLayer` instancias están contenidas en el `GameView` (clase), que hereda de `CCScene`. Caídas Fruity también incluye varias escenas, el primero es el `TitleScene`. Por lo tanto, el `Initialize` crea una instancia de método un `TitleScene` instancia que se pasa al `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

El contenido de Fruity corresponden a las fechas se creó como carátula de baja resolución píxel por motivos estéticos. El `GameView.DesignResolution` se establece para que el juego sea 384 unidades de ancho y alto 512:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Por último, el `GameController` clase proporciona un método estático que se puede llamar mediante cualquier `CCGameScene` para realizar la transición a otro `CCScene`. Este método se usa para desplazarse por la `TitleScene` y `GameScene`.


## <a name="game-entities"></a>Entidades de juegos

Caídas Fruity hace uso del patrón de entidad para la mayoría de los objetos del juego. Para obtener una explicación detallada de este patrón puede encontrarse en el [guiar las entidades de CocosSharp](~/graphics-games/cocossharp/entities.md).

Los objetos de juego de implementación de entidad pueden encontrarse en la carpeta de entidades en el **FruityFalls.Common** proyecto:

![](fruity-falls-images/image2.png "La carpeta de entidades en el proyecto FruityFalls.Common")

Las entidades son objetos que heredan de `CCNode`y puede tener los objetos visuales, colisiones y el comportamiento de cada fotograma.

La `Fruit` objeto representa una entidad de CocosSharp típico: contiene un objeto visual, colisiones y lógica de cada fotograma. Es responsable de inicializar la fruta su constructor:


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


### <a name="fruit-graphics"></a>Gráficos de frutas

El `CreateFruitGraphic` método crea un `CCSprite` de instancia y lo agrega a la `Fruit`. El `IsAntialiased` propiedad se establece en false para conferir el juego pixelada. Este valor se establece en false en todas las `CCSprite` y `CCLabel` instancias a lo largo del juego:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Cada vez que el jugador los toca un `Fruit` instancia con el `Paddle`, el valor del punto de esa fruta aumenta en uno. Esto proporciona una dificultad añadida a los jugadores con experiencia que jugar con frutas para puntos adicionales. El valor del punto de la fruta se muestra mediante el `extraPointsLabel` instancia.

El `CreateExtraPointsLabel` método crea un `CCLabel` de instancia y lo agrega a la `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Caídas Fruity incluye dos tipos diferentes de fruta: cerezas y lemons. El `CreateFruitGraphic` asigna un objeto visual de forma predeterminada, pero los objetos visuales de fruta se pueden cambiar mediante la `FruitColor` propiedad, que a su vez llama a `UpdateGraphics`:


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

La primera instrucción if en `UpdateGraphics` actualiza los gráficos de depuración, que se usan para visualizar las áreas de la colisión. Estos objetos visuales están desactivados antes de que se realiza una versión final del juego, pero se puede mantener en durante el desarrollo para la depuración de leyes físicas. El segundo cambios parte la `graphic.Texture` propiedad mediante una llamada a `CCTextureCache.SharedTextureCache.AddImage`. Este método proporciona acceso a una textura por nombre de archivo. Puede encontrar más información sobre este método en el [Guía de almacenamiento en caché de textura](~/graphics-games/cocossharp/texture-cache.md).

El `extraPointsLabel` color se ajusta para mantener contraste con la imagen de la fruta y su `PositionY` valor se ajusta al centro de la `CCLabel` en la fruta `CCSprite`:

![](fruity-falls-images/image3.png "El color extraPointsLabel se ajusta para mantener contraste con la imagen de frutas y su valor PositionY se ajusta para centrar en las frutas CCSprite CCLabel")

![](fruity-falls-images/image4.png "El color extraPointsLabel se ajusta para mantener contraste con la imagen de frutas y su valor PositionY se ajusta para centrar en las frutas CCSprite CCLabel")


### <a name="collision"></a>Colisión

Caídas Fruity implementa una solución personalizada de colisión con objetos en la carpeta de geometría:

![](fruity-falls-images/image5.png "Caídas Fruity implementa una solución personalizada de colisión con objetos en la carpeta de geometría")

Colisión de caídas Fruity se implementa mediante el `Circle` o `Polygon` objeto, normalmente con uno de estos dos tipos que se agrega como elemento secundario de una entidad. Por ejemplo, el `Fruit` objeto tiene un `Circle` llamado `Collision` que inicializa en su `CreateCollision` método:


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Tenga en cuenta que el `Circle` clase hereda de la `CCNode` clase, para que pueda agregar como elemento secundario a entidades de nuestro juego:


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` es similar a la creación `Circle` creación, como se muestra en el `Paddle` clase:


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

Se trata la lógica de colisión [más adelante en esta guía](#collision).


## <a name="physics"></a>Física

La física en Fruity corresponden a las fechas se puede dividir en dos categorías: movimiento y la colisión. 


### <a name="movement-using-velocity-and-acceleration"></a>Con la velocidad y la aceleración de movimiento

Usa caídas Fruity `Velocity` y `Acceleration` valores para controlar el movimiento de sus entidades, similar a la [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Entidades implementan su lógica de movimiento en un método denominado `Activity`, que se llama una vez por fotograma. Por ejemplo, podemos ver la implementación de movimiento en el `Fruit` clase `Activity` método:

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
El `Fruit` es único en su implementación de arrastrar objetos: un valor que se ralentiza la velocidad en relación con rapidez la fruta está moviendo. Proporciona esta implementación de arrastrar un *velocity terminal*, que es la velocidad máxima que puede dividirse una instancia de fruta. Arrastre también ralentiza el movimiento horizontal de frutas, lo que facilita el juego ligeramente reproducir.

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

Normalmente, los juegos que usan `Velocity` controlar la posición de su no objetos directamente no establece posiciones de la entidad, al menos no después de la inicialización. En lugar de establecer directamente la `Paddle` posición, el `Paddle.HandleInput` método asigna el `desiredLocation` valor:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>Colisión

Caídas Fruity implementa parcialmente realista colisión entre la fruta y otros objetos collidable como el `Paddle` y `GameScene.Splitter`. Para ayudar a depurar colisión, áreas de colisión de caídas Fruity pueden hacerse visibles cambiando el `GameCoefficients.ShowDebugInfo` en el `GameCoefficients.cs` archivo:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Establecer este valor en `true` permite la representación de áreas de conflicto:  

![](fruity-falls-images/image6.png "Este valor en true habilita la representación de áreas de colisión")

Lógica de colisión comienza en el `GameScene.Activity` método:


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

`PerformCollision` Controla el conflicto todos `Fruit` instancias con otros objetos: 


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

#### <a name="fruitvsborders"></a>FruitVsBorders

`FruitVsBorders` colisión realiza su propia lógica de colisión, en lugar de confiar en la lógica contenida en una clase diferente. Esta diferencia existe porque la colisión entre frutas y bordes de la pantalla no es perfectamente sólida: es posible que las frutas en movimiento paddle cuidado enviarse fuera del borde de la pantalla. Frutas rebotará fuera de la pantalla cuando se llama con la interfaz, pero si el Reproductor inserta lentamente fruta moverá más allá del borde y fuera de la pantalla:


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

#### <a name="fruitvsbins"></a>FruitVsBins

El `FruitVsBins` método es responsable de comprobar si las frutas haya caído en una de las dos ubicaciones. Si es así, el Reproductor se otorgó los puntos (si la coincidencia de colores de la fruta/bin) o el juego finaliza (si no coinciden con los colores):


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

#### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle y FruitPolygonCollision

Frutas frente a la paleta y frutas frente a divisor (el área de separación de las dos ubicaciones) colisión dependen de ambas la `FruitPolygonCollision` método. Este método tiene tres partes:

1. Comprobar si los objetos en conflicto
1. Mover los objetos (solo la fruta Fruity cae) por lo que ya no se superpongan
1. Ajustar la velocidad de los objetos (solo la fruta Fruity cae) para simular un mensaje devuelto que el código siguiente muestra los puntos mencionados anteriormente:


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

Respuesta de colisión de caídas Fruity es medias: sólo la fruta velocidad y la posición se ajustan. Conviene tener en cuenta que otros juegos que tenga colisión, lo que afecta a ambos objetos implicados, como un carácter de insertar un boulder o dos coches bloquea en Sí.

Las matemáticas detrás de la lógica de colisión contenida en el `Polygon` y `CollisionResponse` clases queda fuera del ámbito de esta guía, pero como escrito, estos métodos se pueden usar para muchos tipos de juegos. El polígono y `CollisionResponse` clases admiten incluso polígonos convexos y no rectangulares, por lo que este código se puede usar para muchos tipos de juegos.

 


## <a name="game-content"></a>Contenido del juego

El material gráfico Fruity caídas distingue inmediatamente el juego de BouncingGame. Aunque los diseños de juegos son similares, reproductores disfrutarán de inmediato una diferencia en el aspecto de los dos juegos. Jugadores a menudo deciden si desea probar un juego por los objetos visuales. Por lo tanto, es sumamente importante que los desarrolladores invierten recursos en realizar un atractivo juego.

El arte de Fruity corresponden a las fechas se creó con los siguientes objetivos:

 - Tema coherente
 - Énfasis en un solo carácter: el objeto monkey de Xamarin
 - Colores brillantes para crear un relajar divertida experiencia
 - Contraste entre el primer y segundo plano, por lo que son fáciles de controlar visualmente los objetos de juego
 - Capacidad para crear efectos visuales simples sin animaciones con mucha actividad de recursos


### <a name="content-location"></a>Ubicación del contenido

Caídas Fruity incluye todo su contenido en la carpeta imágenes en el proyecto de Android:

![](fruity-falls-images/image7.png "Caídas Fruity incluye todo su contenido en la carpeta imágenes en el proyecto de Android")

Estos mismos archivos vinculados en el proyecto de iOS para evitar la duplicación y afectarán a ambos proyectos es así cambios en los archivos:

![](fruity-falls-images/image8.png "Estos mismos archivos vinculados en el proyecto de iOS para evitar la duplicación y afectarán a ambos proyectos es así cambios en los archivos")

Es importante destacar que el contenido no está incluido en el **Ld** o **Hd** carpetas, que forman parte de la plantilla de CocosSharp predeterminada. El **Ld** y **Hd** carpetas están diseñadas para usarse para juegos que proporcionan dos conjuntos de contenido, uno para dispositivos de menor resolución, como teléfonos y otra para los dispositivos de resolución superior, como tabletas. El arte de caídas Fruity es creado deliberadamente con un pixelado estético, por lo que no necesita proporcionar contenido para distintos tamaños de pantalla. Por lo tanto, el **Ld** y **Hd** carpetas se han quitado completamente del proyecto.


### <a name="gamescene-layering"></a>GameScene disposición en capas

Como se mencionó anteriormente en esta guía, la `GameScene` es responsable de todas las creación de instancias de objeto de juego, colocar y lógica entre objeto (como colisión). Todos los objetos se agregan a uno de los cuatro `CCLayer` instancias:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Estos cuatro capas se crean en el `CreateLayers` método. Tenga en cuenta que se agregan a la `GameScene` en orden de atrás al frente:


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

Una vez creadas las capas, todos los objetos visuales se agregan a las capas utilizando las `AddChild` método, como se muestra en el `CreateBackground` método:


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


### <a name="vine-entity"></a>Entidad VID

El `Vine` entidad se usa exclusivamente para el contenido: no tiene efecto en el juego. Se compone de veinte `CCSprite` instancias, un número seleccionado mediante prueba y error para asegurarse de que la vid siempre llega a la parte superior de la pantalla:


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

La primera `CCSprite` se coloca para la posición de la vid coincida con el borde inferior. Esto se logra estableciendo el AnchorPoint en `new CCPoint(.5f, 0)`. El `AnchorPoint` propiedad usa *normalizado coordenadas* el intervalo entre 0 y 1, independientemente del tamaño de la `CCSprite`:

![](fruity-falls-images/image9.png "La propiedad AnchorPoint usa coordenadas normalizadas el intervalo comprendido entre 0 y 1, independientemente del tamaño de la CCSprite")

Sprites VID subsiguientes se sitúan mediante el `graphic.ContentSize.Height` valor, que devuelve el alto de la imagen en píxeles. El diagrama siguiente muestra cómo el gráfico de vid iconos hacia arriba:

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

La vid instancias en el `Paddle` entidad también tienen el comportamiento de giro interesante. Puesto que la `Paddle` entidad como un todo gira según la entrada de jugadores (que esta guía se explica más detalladamente a continuación), el `Vine` instancias también se ve afectadas por este giro. Sin embargo, la rotación del `Vine` instancias junto con el `Paddle` produce un efecto visual no deseado, como se muestra en la siguiente animación:

![](fruity-falls-images/image11.gif "Sin embargo, la rotación de las instancias de vid junto con la paleta produce un efecto visual no deseado como se muestra en la siguiente animación")

Para contrarrestar la `Paddle` rotación, el `leftVine` y `rightVine` se giran las instancias de la dirección opuesta de la paleta, como se muestra en el `Paddle.Activity` método:


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

Tenga en cuenta que una pequeña cantidad de rotación se agrega a la vid a través de la `vineAngle` coeficiente. Este valor puede cambiarse para ajustar cuánto se gira el vines.


## <a name="gamecoefficients"></a>GameCoefficients

Todos los juegos buena es el producto de iteración, por lo que se encuentra Fruity incluye una clase denominada `GameCoefficients` que controla cómo se reproduce el juego. Esta clase contiene expresivas variables que se usan en todo el juego para el control física, diseño, generando y puntuación.

Por ejemplo, la física fruta se controla mediante las siguientes variables:


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

Tal como indican los nombres, estas variables se pueden usar para ajustar la rapidez fruta caídas cómo horizontal movimiento ralentiza con el tiempo y rebote de la paleta.

Juego coeficientes que se almacenan en archivos de código o datos (por ejemplo, XML) pueden ser especialmente valiosos para los equipos con diseñadores de juegos que no son programadores.

La `GameCoefficients` clase también incluye valores para activar la información de depuración como la creación de áreas de información o conflicto:


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


## <a name="conclusion"></a>Conclusión

Esta guía se han explorado el juego Fruity corresponden a las fechas. Tratan conceptos como contenido, física y la administración de estado del juego.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Proyecto completado (ejemplo)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
