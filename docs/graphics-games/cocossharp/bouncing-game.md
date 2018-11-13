---
title: Detalles de BouncingGame
description: Este documento proporciona un tutorial para la creación de BouncingGame, un juego de bola que rebota simple creado con CocosSharp.
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 9fd6c9108695f58bd69a1c4aa307ca2e4be6dede
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528746"
---
# <a name="bouncinggame-details"></a>Detalles de BouncingGame

> [!TIP]
> El código de BouncingGame puede encontrarse en el [ejemplos de Xamarin](https://developer.xamarin.com/samples/mobile/BouncingGame/). Para mejor seguir esta guía, descargar y explorar el código como la Guía hace referencia a él.

Este tutorial muestra cómo implementar un juego de bola que rebota simple con CocosSharp. 

 - Descomprimiendo contenido juegos
 - Elementos visuales de CocosSharp comunes
 - Agregar los elementos visuales para `GameLayer`
 - Implementar la lógica de cada fotograma

Nuestro juego terminado tendrá este aspecto:

![BouncingGame, completado](bouncing-game-images/image1.png "BouncingGame, completado")

## <a name="unzipping-game-content"></a>Descomprimiendo contenido juegos

Los desarrolladores de juegos a menudo usan el término *contenido* para hacer referencia a archivos de código que normalmente son creados por visuales artistas, diseñadores de juego o diseñadores de audio. Tipos de contenido comunes incluyen los archivos que se usa para mostrar los objetos visuales, reproducir un sonido o controlar el comportamiento de inteligencia artificial (IA). Desde la perspectiva de un equipo de desarrollo de juegos, que no son programadores suelen crear contenido. Nuestro juego incluye dos tipos de contenido:

 - archivos PNG para definir el aspecto de la bola y la paleta.
 - Un archivo único xnb para definir la fuente utilizada por la pantalla de puntuación (se explica con más detalle cuando se agrega la siguiente pantalla de puntuación)

Este contenido puede usada aquí puede encontrarse en [contenido zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true). Necesitaremos estos archivos descargado y descomprimido en una ubicación que se tendrá acceso a más adelante en este tutorial.

## <a name="common-cocossharp-visual-elements"></a>Elementos visuales de CocosSharp comunes

CocosSharp proporciona una serie de clases que se usan para mostrar los objetos visuales. Algunos elementos están visibles directamente, mientras que otros se usan para la organización. En el juego, vamos a usar lo siguiente:

 - `CCNode` – Clase base para todos los objetos visuales en CocosSharp. El `CCNode` clase contiene un `AddChild` función que se puede usar para construir una jerarquía de elementos primarios y secundarios, también se denomina un *árbol visual* o *gráfico de escena*. Todas las clases que se mencionan a continuación se heredan de `CCNode`.
 - `CCScene` : Raíz del árbol visual para todos los juegos de CocosSharp. Todos los elementos visuales deben formar parte de un árbol visual con un `CCScene` en la raíz, o que no son visibles.
 - `CCLayer` : Objetos contenedor de objeto visual, como `CCSprite`. Como el nombre implica, el `CCLayer` clase se utiliza para controlar cómo visual capa de elementos entre sí.
 - `CCSprite` : Muestra una imagen o una parte de una imagen. `CCSprite` las instancias se pueden colocar, cambia el tamaño y ofrecen una serie de efectos visuales.
 - `CCLabel` : Muestra una cadena en la pantalla. La fuente utilizada por `CCLabel` se define en un archivo xnb. Trataremos xnbs en más detalle a continuación.

Para entender cómo se usan los diferentes tipos, nos centraremos en un único `CCSprite`. Elementos visuales deben agregarse a un `CCLayer`, y el árbol visual debe tener un `CCScene` en su raíz. Por lo tanto, la relación de elementos primarios y secundarios para una sola `CCSprite` sería `CCScene`  >  `CCLayer`  >  `CCSprite`.

## <a name="adding-visual-elements-to-gamelayer"></a>Agregar elementos visuales a GameLayer

### <a name="adding-the-paddle-sprite"></a>Agregar el sprite paddle

También se establece inicialmente en segundo plano del juego a negro y también agrega una única `CCSprite` representar en la pantalla. Modificar el `GameLayer` clase para agregar un `CCSprite` como sigue:

```csharp
using System;
using System.Collections.Generic;
using CocosSharp;
namespace BouncingGame
{
    public class GameLayer : CCLayer
    {
        CCSprite paddleSprite;
        public GameLayer () : base(CCColor4B.Black)
        {
            // "paddle" refers to the paddle.png image
            paddleSprite = new CCSprite ("paddle");
            paddleSprite.PositionX = 100;
            paddleSprite.PositionY = 100;
            AddChild (paddleSprite);
        }
        protected override void AddedToScene ()
        {
            base.AddedToScene ();
            // Use the bounds to layout the positioning of the drawable assets
            CCRect bounds = VisibleBoundsWorldspace;
            // Register for touch events
            var touchListener = new CCEventListenerTouchAllAtOnce ();
            touchListener.OnTouchesEnded = OnTouchesEnded;
            AddEventListener (touchListener, this);
        }
        void OnTouchesEnded (List<CCTouch> touches, CCEvent touchEvent)
        {
            if (touches.Count > 0)
            {
                // Perform touch handling here
            }
        }
    }
}
```

El código anterior crea una sola `CCSprite` y lo agrega como elemento secundario de la `GameLayer`. El `CCSprite` constructor nos permite definir el archivo de imagen para usarla como una cadena. Nuestro código indica a CocosSharp para buscar un archivo denominado `paddle` (la extensión se omite, lo que hablaremos más adelante en esta guía). CocosSharp va a buscar los nombres de archivo `paddle` en la carpeta raíz de contenido (que es **contenido**), así como las carpetas que se agrega a la `gameView.ContentManager.SearchPaths` (se describe en la sección anterior).

Vamos a agregar los archivos directamente a la raíz **contenido** carpeta para iOS y Android. Para ello, haga clic o pulse Control en el **contenido** carpeta en el proyecto de iOS y seleccione **agregar** > **agregar archivos...** Vaya a donde se descomprimió el contenido anterior y seleccione **paddle.png**. Si se le pregunta acerca de cómo agregar el archivo a la carpeta, debemos seleccionamos la **copia** opción:

![Agregar archivo al cuadro de diálogo de carpeta](bouncing-game-images/image2.png "el archivo agregar al cuadro de diálogo de carpeta")

A continuación, vamos a agregar el archivo al proyecto de Android. Haga clic o pulse en la carpeta de contenido del Control (que se encuentra en la **activos** carpetas en proyectos de Android) y seleccione **agregar** > **agregar archivos...** . Esta vez, navegue hasta el proyecto de iOS **contenido** carpeta. Cuando se le pregunte acerca de cómo agregar el archivo, seleccione el **agregar un vínculo** opción:

![Agregar archivo al cuadro de diálogo de carpeta](bouncing-game-images/addalink.png "agregar el archivo al cuadro de diálogo de carpeta")

Trataremos de por qué archivos se tenían que agregarse a ambos proyectos a continuación. Cada proyecto **contenido** carpeta contiene ahora el **paddle.png** archivo:

![El contenido de la carpeta de contenido](bouncing-game-images/image3.png "el contenido de la carpeta de contenido")

Si se ejecuta el juego veremos el `CCSprite` va a dibujar:

![La paleta, se dibuja en la pantalla](bouncing-game-images/image4.png "la paleta, se dibuja en la pantalla")

### <a name="file-details"></a>Detalles de archivo

Hasta ahora hemos agregado un único archivo al proyecto, y el proceso fue bastante sencillo. Simplemente agregamos el **paddle.png** del archivo a la **contenido** carpetas y al que hace referencia en el código. Dedique un momento a examinar algunas consideraciones al trabajar con archivos de CocosSharp.

#### <a name="capitalization"></a>Uso de mayúsculas

Tenga en cuenta que el nombre de archivo y la cadena se usa en código para tener acceso al archivo estén en minúsculas. Esto es porque algunas plataformas (por ejemplo, el simulador de iOS y de escritorio de Windows) distinguen mayúsculas de minúsculas, mientras que otras plataformas (por ejemplo, el dispositivo iOS y Android) distinguen mayúsculas de minúsculas. Se van a usar todos los archivos en minúsculas para el resto de este tutorial para que los archivos y código permanecen como multiplataforma como sea posible.

#### <a name="extensions"></a>Extensiones

El constructor para crear el Sprite no incluye la extensión ".png" cuando se hace referencia al archivo de paleta. La extensión de archivos normalmente se omite cuando trabajan en proyectos de CocosSharp como extensiones de archivo para el mismo tipo de recurso puede diferir entre plataformas. Por ejemplo, archivos de audio pueden ser de formatos de archivo .aiff,. mp3 o .wma, dependiendo de la plataforma. Salir de la extensión permite el mismo código para que funcione en todas las plataformas, independientemente de la extensión de archivo.

#### <a name="content-in-platform-specific-projects"></a>El contenido de los proyectos específicos de plataforma

A diferencia de los archivos de código que pueden estar en una PCL, los archivos de contenido deben agregarse a cada proyecto específico de la plataforma. CocosSharp requiere esto por dos motivos:

1. Cada plataforma tiene distintos **acciones de compilación**. El contenido agregado a los proyectos de iOS debe usar el **BundleResource** acción de compilación. El contenido agregado a los proyectos de Android debe usar el **AndroidAsset** acción de compilación.
2. Algunas plataformas requieren formatos de archivo específicos de la plataforma. Por ejemplo, los archivos de fuentes .xnb difieren entre iOS y Android, como veremos más adelante en este tutorial.

Si no, un formato de archivo difieren entre plataformas (por ejemplo, .png), cada plataforma puede usar el mismo archivo, donde una o varias plataformas pueden vincular el archivo desde la misma ubicación.

## <a name="orientation"></a>Orientación

Al igual que cualquier otra aplicación, pueden ejecutar aplicaciones de CocosSharp en orientación vertical u horizontal. Se va a ajustar el juego se ejecute en modo de sólo vertical. En primer lugar, cambiaremos el código de resolución en el juego para controlar una relación de aspecto vertical. Para ello, modifique la `width` y `height` valores en el `LoadGame` método en el `ViewController` clase en iOS y `MainActivity` clase en Android:

```csharp
void LoadGame (object sender, EventArgs e)
{
    CCGameView gameView = sender as CCGameView;

    if (gameView != null)
    {
        var contentSearchPaths = new List<string> () { "Fonts", "Sounds" };
        CCSizeI viewSize = gameView.ViewSize;

        int width = 768;
        int height = 1027;
    // ...
```

A continuación, deberá modificar cada proyecto específico de plataforma para ejecutar en modo vertical.

### <a name="ios-orientation"></a>orientación de iOS

Para modificar la orientación del proyecto de iOS, seleccione el **Info.plist** de archivos en el **BouncingGame.iOS** del proyecto y cambiar **iPhone/iPod información sobre implementación** y el **iPad información sobre implementación** para incluir solo las orientaciones vertical:

![Opciones de orientación](bouncing-game-images/image5.png "opciones de orientación")

### <a name="android-orientation"></a>Orientación de Android

Para modificar la orientación del proyecto Android, abra el archivo MainActivity.cs en el proyecto BouncingGame.Android. Modifique la definición de atributo de la actividad por lo que especifica solo una orientación vertical como sigue:

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>Sistema de coordenadas predeterminado

Nuestro código, que crea una instancia de un `CCSprite`, Establece la `PositionX` y `PositionY` los valores a 100. De forma predeterminada, esto significa que el `CCSprite` center se colocará en 100 píxeles hacia arriba y hacia la derecha de la parte inferior izquierda de la pantalla. Se puede modificar el sistema de coordenadas adjuntando un `CCCamera` a la `CCLayer`. No trabajamos con `CCCamera` en este proyecto, pero más información sobre `CCCamera` puede encontrarse en el [documentos de la API de CocosSharp](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/).

Los 100 píxeles mencionados anteriormente píxeles "juegos" en lugar de píxeles en el hardware. Esto significa que se ejecuta el mismo juego en un dispositivo de una resolución diferente (por ejemplo, un iPad frente a un iPhone) dará como resultado en los objetos que se coloca y el tamaño adecuado en relación con la pantalla física. En concreto, área visible del juego siempre será 1024 píxeles de alto y ancho de 768 píxeles, ya que esta es la resolución, hemos especificado anteriormente en el `LoadGame` método.

## <a name="adding-the-ball-sprite"></a>Agregar el sprite de bola

Ahora que estamos familiarizados con los conceptos básicos de trabajar con `CCSprite`, vamos a agregar el segundo `CCSprite` : una bola. Se estará siguiendo los pasos que son muy similares a cómo se crea el remo `CCSprite`. 

En primer lugar, vamos a agregar el **ball.png** imagen desde la carpeta descomprimida en el proyecto de iOS **contenido** carpeta. Seleccione esta opción para **copia** el archivo a la **contenido** directory. Siga los pasos anteriores para agregar un vínculo a la **ball.png** archivo en el proyecto de Android.

A continuación cree el `CCSprite` para este bola agregando un miembro denominado `ballSprite` a la `GameScene` clase, así como código de creación de instancias para el `ballSprite`. Cuando termine la `GameLayer` clase tendrá un aspecto similar al siguiente:

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
    }
```

## <a name="adding-the-score-label"></a>Agregar la etiqueta de puntuación

El último elemento visual que vamos a agregar al juego es una `CCLabel` para mostrar el número de veces que el usuario ha devuelto correctamente la bola. El `CCLabel` usa una fuente especificada en el constructor para cadenas de presentación en pantalla.

Agregue el código siguiente para crear un `CCLabel` en la instancia `GameLayer`. Una vez finalizado el código debe ser similar al siguiente:

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    CCLabel scoreLabel;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "Arial", 20, CCLabelFormat.SystemFont);
        scoreLabel.PositionX = 50;
        scoreLabel.PositionY = 1000;
        scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
        AddChild (scoreLabel);
    }
    // ...
```

Tenga en cuenta que está usando el scoreLabel un `AnchorPoint` de `CCPoint.AnchorUpperLeft`, lo que significa que el `PositionX` y `PositionY` valores definen su posición superior izquierda. Esto nos permite posición la `scoreLabel` en relación con la parte superior izquierda de la pantalla sin tener que considerar las dimensiones de la etiqueta.

## <a name="implementing-every-frame-logic"></a>Implementar la lógica de cada fotograma

Hasta ahora, el juego presenta una escena estática. Iremos agregando lógica para controlar el movimiento de objetos de la escena mediante la adición de código que actualiza la posición de los objetos con una alta frecuencia. En este caso, el código se ejecutará 60 veces por segundo - también se denomina sesenta *marcos* por segundo (a menos que el hardware no puede controlar esto con frecuencia la actualización). En concreto, agregaremos lógica para que la bola se dividen y rebote frente a la paleta, para mover la paleta según los datos proporcionados y para actualizar la puntuación del jugador cada vez que la bola golpee la paleta.

El `Schedule` método, que proporciona el `CCNode` class, nos permite agregar lógica de cada fotograma del juego. Vamos a agregar el código después `// New code` al constructor GameLayer:

```csharp
public GameLayer () : base (CCColor4B.Black)
{
    // "paddle" refers to the paddle.png image
    paddleSprite = new CCSprite ("paddle");
    paddleSprite.PositionX = 100;
    paddleSprite.PositionY = 100;
    AddChild (paddleSprite);
    ballSprite = new CCSprite ("ball");
    ballSprite.PositionX = 320;
    ballSprite.PositionY = 600;
    AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "arial", 22, CCLabelFormat.SpriteFont);
    scoreLabel.PositionX = 50;
    scoreLabel.PositionY = 1000;
    scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
    AddChild (scoreLabel);
    // New code:
    Schedule (RunGameLogic);
}
```

Ahora, cree un `RunGameLogic` método en el `GameLayer` (clase), que contendrá toda la lógica de cada fotograma:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

El parámetro float define cuánto tiempo es el marco en segundos. Vamos a usar este valor al implementar el movimiento de la bola.

### <a name="making-the-ball-fall"></a>Hacer que la bola se dividen

Podemos hacer que la bola se dividen por el código de gravedad implementación manualmente o mediante la funcionalidad integrada de Box2D en CocosSharp. El motor de simulación de leyes físicas Box2D está disponible para los juegos de CocosSharp. Es muy eficaz y eficiente, pero requiere escribir código de programa de instalación. Puesto que la simulación de leyes físicas es bastante simple, aquí se llevará a cabo manualmente.

Para implementar la gravedad necesitamos la X actual del almacén y la velocidad Y de la bola. Vamos a agregar dos miembros para el `GameLayer` clase:

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

A continuación podemos implementar la lógica de caída `RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>Mover el remo con entrada táctil

Ahora que la bola se queda, vamos a agregar movimiento horizontal a la interfaz mediante el uso de la `CCEventListenerTouchAllAtOnce` objeto. Este objeto proporciona una serie de eventos relacionados con la entrada. En este caso, queremos recibir una notificación si los puntos de toque mover por lo que podemos ajustar la posición de la paleta. El `GameLayer` ya crea una instancia de un `CCEventListenerTouchAllAtOnce`, por lo que necesitamos asignar el `OnTouchesMoved` delegar. Para ello, modifique el método AddedToScene como sigue:

```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of the drawable assets
    CCRect bounds = VisibleBoundsWorldspace;
    // Register for touch events
    var touchListener = new CCEventListenerTouchAllAtOnce ();
    touchListener.OnTouchesEnded = OnTouchesEnded;
    // new code:
    touchListener.OnTouchesMoved = HandleTouchesMoved;
    AddEventListener (touchListener, this);
}
```

A continuación, implementaremos `HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>Implementación de colisión de bola

Si se ejecuta el juego Ahora veremos que la bola cae a través de la paleta. Implementaremos *colisión* (lógica para reaccionar a los objetos del juego de superposición) en el código de cada fotograma. Puesto que mover los objetos modificados coloca cada fotograma, comprobación de colisión es normalmente también se realizan cada fotograma. También agregaremos velocidad en el eje X cuando la bola golpea la paleta para agregar un reto para el juego, pero esto significa que necesitamos para impedir que la bola se mueve más allá de los bordes de la pantalla. Vamos a hacer en el `RunGameLogic` código después de aplicar la velocidad a la `ballSprite` después `// New Code`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
    // New Code:
    // Check if the two CCSprites overlap...
    bool doesBallOverlapPaddle = ballSprite.BoundingBoxTransformedToParent.IntersectsRect(
        paddleSprite.BoundingBoxTransformedToParent);
    // ... and if the ball is moving downward.
    bool isMovingDownward = ballYVelocity < 0;
    if (doesBallOverlapPaddle && isMovingDownward)
    {
        // First let's invert the velocity:
        ballYVelocity *= -1;
        // Then let's assign a random value to the ball's x velocity:
        const float minXVelocity = -300;
        const float maxXVelocity = 300;
        ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    }
    // First let’s get the ball position:   
    float ballRight = ballSprite.BoundingBoxTransformedToParent.MaxX;
    float ballLeft = ballSprite.BoundingBoxTransformedToParent.MinX;
    // Then let’s get the screen edges
    float screenRight = VisibleBoundsWorldspace.MaxX;
    float screenLeft = VisibleBoundsWorldspace.MinX;
    // Check if the ball is either too far to the right or left:    
    bool shouldReflectXVelocity = 
        (ballRight > screenRight && ballXVelocity > 0) ||
        (ballLeft < screenLeft && ballXVelocity < 0);
    if (shouldReflectXVelocity)
    {
        ballXVelocity *= -1;
    }
}
```

## <a name="adding-scoring"></a>Agregar puntuación

Ahora que el juego es reproducible, el último paso es agregar lógica de puntuación. En primer lugar, vamos a agregar un miembro de puntuación a la clase GameLayer denominada `score`:

```csharp
int score;
```

Utilizaremos esta variable para realizar un seguimiento de la puntuación del jugador y mostrar mediante el `scoreLabel`. Hacer esto agregue el código siguiente dentro de la instrucción if en `RunGameLogic` cuando la bola y paddle se superponen:

```csharp
// ...
if (doesBallOverlapPaddle && isMovingDownward)
{
    // First let's invert the velocity:
    ballYVelocity *= -1;
    // Then let's assign a random to the ball's x velocity:
    const float minXVelocity = -300;
    const float maxXVelocity = 300;
    ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    // New code:
    score++;
    scoreLabel.Text = "Score: " + score;
}
// ...
```

Ahora podemos ejecutar el juego y vea que el juego muestra una puntuación que se incrementa según la bola rebota fuera de la interfaz:

![El juego completo, con una puntuación incrementa](bouncing-game-images/image1.png "el juego completo, con una puntuación de incremento")

## <a name="summary"></a>Resumen

En este tutorial presenta la creación de un juego multiplataforma con gráficos, física y uso de CocosSharp de entrada. Es el primer paso de introducción al desarrollo de juegos de CocosSharp. Hemos analizado algunas de las clases más comunes en CocosSharp, cómo construir un árbol visual para que los objetos se presentan correctamente y cómo implementar la lógica del juego cada fotograma.

En este tutorial trata solamente una pequeña parte de lo que ofrece el motor de juego de CocosSharp. Para obtener información y tutoriales sobre otros temas de CocosSharp, consulte [el resto de las guías de CocosSharp](~/graphics-games/cocossharp/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Juego completo (ejemplo)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Contenido de juego (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
