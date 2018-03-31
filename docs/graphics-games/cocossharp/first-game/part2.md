---
title: Implementar el juego rebote
description: Este tutorial muestra cómo agregar lógica de juego y el contenido a una plantilla vacía para crear un juego completo llamado BouncingGame.
ms.topic: article
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 584ae03a3a773ae7e16fa7a24b9dbfa6c5056342
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="implementing-the-bouncing-game"></a>Implementar el juego rebote

_Este tutorial muestra cómo agregar lógica de juego y el contenido a una plantilla vacía para crear un juego completo llamado BouncingGame._

La parte anterior de este tutorial se ha explicado cómo crear una solución de CocosSharp con los proyectos para iOS y Android desarrollo. Esta guía continuará implementando un juego completo, que abarcan lo siguiente:

 - Descompresión nuestro contenido de juego
 - Elementos visuales de CocosSharp comunes
 - Agregar elementos visuales a `GameLayer`
 - Implementa la lógica de cada fotograma

Nuestro juego terminado tendrá un aspecto similar al siguiente:

![](part2-images/image1.png "El juego terminado tendrá un aspecto similar al siguiente")


# <a name="unzipping-our-game-content"></a>Descompresión nuestro contenido de juegos

Los desarrolladores de juegos a menudo usa el término *contenido* para hacer referencia a los archivos de código que normalmente se crean mediante visuales intérpretes, diseñadores de juegos o diseñadores de audio. Tipos comunes de contenido son archivos que se utilizan para mostrar objetos visuales, reproducir un sonido o controlar el comportamiento de inteligencia artificial (AI). Desde la perspectiva de un equipo de desarrollo de juegos, no son programadores normalmente crea el contenido. Nuestro juego incluye dos tipos de contenido:

 - Archivos PNG para definir cómo se muestran la bola y la paleta.
 - Un único archivo XNB para definir la fuente utilizada por nuestra presentación de puntuación (se explica con más detalle cuando se agrega la puntuación elementos mostrados a continuación)

Este contenido que se usa aquí se puede encontrar en [contenido zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true). Necesitaremos estos archivos descargado y descomprimido en una ubicación que se tendrá acceso a más adelante en este tutorial.


# <a name="common-cocossharp-visual-elements"></a>Elementos comunes de CocosSharp Visual

CocosSharp proporciona una serie de clases que se usan para mostrar los objetos visuales. Algunos elementos aparecen directamente, mientras que otros se utilizan para la organización. En el juego, vamos a usar lo siguiente:

 - `CCNode` – Clase base para todos los objetos visuales en CocosSharp. El `CCNode` clase contiene una `AddChild` función que se puede usar para construir una jerarquía de elementos primarios y secundarios, también se denomina un *árbol visual* o *gráfico de escena*. Todas las clases que se mencionan más abajo se heredan de `CCNode`.
 - `CCScene` : La raíz del árbol visual para todos los juegos de CocosSharp. Todos los elementos visuales deben formar parte de un árbol visual con un `CCScene` en la raíz, o no serán visibles.
 - `CCLayer` : Objetos de contenedor visual, como `CCSprite`. Como su nombre indica, la `CCLayer` clase se utiliza para controlar el modo en que visual capa elementos uno sobre otro.
 - `CCSprite` : Muestra una imagen o una parte de una imagen. `CCSprite` las instancias se pueden colocar, cambiar de tamaño y ofrecen una serie de efectos visuales.
 - `CCLabel` : Muestra una cadena en la pantalla. La fuente utilizada por `CCLabel` se define en un archivo XNB. Trataremos XNBs con más detalle a continuación.

Para comprender cómo se usan los diferentes tipos utilizaremos una sola `CCSprite`. Elementos visuales deben agregarse a un `CCLayer`, y el árbol visual debe tener un `CCScene` en la raíz. Por lo tanto, la relación de elementos primarios/secundarios para un único `CCSprite` sería `CCScene`  >  `CCLayer`  >  `CCSprite`.


# <a name="adding-visual-elements-to-gamelayer"></a>Agregar elementos visuales a GameLayer


## <a name="adding-the-paddlesprite"></a>Agregar el paddleSprite

Inicialmente se podrá establecer el fondo del juego a negro y también agregar una sola `CCSprite` de representación en la pantalla. Modificar el `GameLayer` clase para agregar una `CCSprite` como se indica a continuación:


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
            // Use the bounds to layout the positioning of our drawable assets
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

El código anterior crea una sola `CCSprite` y lo agrega como un elemento secundario de la `GameLayer`. El `CCSprite` constructor nos permite definir el archivo de imagen para utilizarlo como una cadena. El código indica CocosSharp para buscar un archivo denominado `paddle` (la extensión se omite, que trataremos más adelante en esta guía). CocosSharp buscará los nombres de archivo `paddle` en la carpeta de contenido raíz (que es **contenido**), así como las carpetas que se agrega a la `gameView.ContentManager.SearchPaths` (descrito en la sección anterior).

Vamos a agregar los archivos directamente a la raíz **contenido** carpeta para iOS y Android. Para ello, menú contextual o Control, haga clic en el **contenido** carpeta en el proyecto de iOS y seleccione **agregar** > **agregar archivos...** Navegue hasta donde se descomprimió el contenido anterior y seleccione **paddle.png**. Si se le pregunta acerca de cómo agregar el archivo a la carpeta, debemos seleccionamos la **copia** opción:

![](part2-images/image2.png "Si se le pregunta acerca de cómo agregar el archivo a la carpeta, seleccione la opción de copia")

A continuación, vamos a agregar el archivo al proyecto de Android. Menú contextual o Control y haga clic en la carpeta de contenido (que se encuentra en la **activos** carpeta proyectos Android) y seleccione **agregar** > **agregar archivos...** . Esta vez, navegue hasta el proyecto de iOS **contenido** carpeta. Cuando se le pregunte acerca de cómo agregar el archivo, seleccione la **agregar un vínculo** opción:

![](part2-images/addalink.png "Cuando se le pregunte acerca de cómo agregar el archivo, seleccione Agregar una opción de link")

Analizaremos por qué archivos se tenían que agregar a los dos proyectos a continuación. Cada proyecto **contenido** carpeta contienen ahora la **paddle.png** archivo:

![](part2-images/image3.png "Carpeta de contenido de cada proyecto contiene ahora el archivo de paddle.png")

Si se ejecuta el juego veremos nuestra `CCSprite` que se va a dibujar:

![](part2-images/image4.png "Si se ejecuta el juego, se dibujará el CCSprite")


## <a name="file-details"></a>Detalles del archivo

Hasta ahora hemos agregado un único archivo para el proyecto, y el proceso fue bastante sencilla. Agregamos simplemente la **paddle.png** del archivo a nuestro **contenido** carpetas y al que hace referencia en el código. Dedique un momento a examinar algunas consideraciones al trabajar con archivos en CocosSharp.

### <a name="capitalization"></a>Uso de mayúsculas

Tenga en cuenta que el nombre de archivo y la cadena se utiliza en código para tener acceso al archivo son ambos minúscula. Esto ocurre porque algunas plataformas (por ejemplo, el simulador de iOS y el escritorio de Windows) se distingue entre mayúsculas y minúsculas, mientras que otras plataformas (por ejemplo, el dispositivo iOS y Android) distinguen mayúsculas de minúsculas. Se van a usar todos los archivos de minúsculas para el resto de este tutorial para que sigan estando nuestros archivos y código como multiplataforma como sea posible.

### <a name="extensions"></a>Extensiones

El constructor para crear el objeto Sprite no incluye la extensión "PNG" cuando se hace referencia al archivo de la paleta. La extensión de archivos normalmente se omite cuando se trabaja en proyectos de CocosSharp como extensiones de archivo para el mismo tipo de recurso puede diferir entre plataformas. Por ejemplo, archivos de audio pueden ser de formatos de archivo .aiff,. mp3 o .wma, dependiendo de la plataforma. Si se deja desactivada la extensión permitirá el mismo código para que funcione en todas las plataformas, independientemente de la extensión de archivo.

### <a name="content-in-platform-specific-projects"></a>Contenido en proyectos específicos de plataforma

A diferencia de la mayoría de los archivos de código que pueden estar en un una PCL, contenida de archivos deben agregarse a cada proyecto específico de la plataforma. CocosSharp requiere esto por dos motivos:

1. Cada plataforma tiene distintos **acciones de compilación**. Contenido que se agregará a los proyectos de iOS debe usar el **BundleResource** acción de compilación. Debe usar el contenido agregado para proyectos Android el **AndroidAsset** acción de compilación.
2. Algunas plataformas requieren formatos de archivo específicos de la plataforma. Por ejemplo, los archivos de fuente .xnb difieren entre iOS y Android, como veremos más adelante en este tutorial.

Si no, un formato de archivo difieren entre plataformas (por ejemplo, .png), cada plataforma puede usar el mismo archivo, donde una o varias plataformas pueden vincular el archivo desde la misma ubicación.

# <a name="orientation"></a>Orientación

Al igual que cualquier otra aplicación, pueden ejecutar aplicaciones CocosSharp en orientación vertical u horizontal. Se va a ajustar el juego para que se ejecute en modo de solo vertical. En primer lugar, cambiaremos el código de resolución en el juego para controlar una relación de aspecto vertical. Para ello, modifique la `width` y `height` valores en el `LoadGame` método en el `ViewController` clase en iOS y `MainActivity` clase en Android:


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
    ...
```

A continuación se necesitará modificar cada proyecto específico de plataforma para que se ejecute en modo vertical.


## <a name="ios-orientation"></a>Orientación de iOS

Para modificar la orientación del proyecto de iOS, seleccione la **Info.plist** un archivo en el **BouncingGame.iOS** del proyecto y cambiar **iPhone/iPod información de implementación** y la **iPad información de implementación** para incluir sólo las orientaciones vertical:

![](part2-images/image5.png "Cambiar iPhone/iPod información de implementación y la información de implementación para incluir sólo las orientaciones vertical iPad")


## <a name="android-orientation"></a>Orientación de Android

Para modificar la orientación del proyecto Android, abra el archivo MainActivity.cs en el proyecto BouncingGame.Android. Modifique la definición de atributo de la actividad por lo que especifica solo una orientación vertical como se indica a continuación:


```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```


# <a name="default-coordinate-system"></a>Sistema de coordenadas predeterminado

El código que crea una instancia de un `CCSprite`, Establece la `PositionX` y `PositionY` valores a 100. De forma predeterminada, esto significa que el `CCSprite` center se colocarán en 100 píxeles hacia arriba y hacia la derecha de la parte inferior izquierda de la pantalla. Se puede modificar el sistema de coordenadas adjuntando un `CCCamera` a la `CCLayer`. No trabajamos con `CCCamera` en este proyecto, pero desea obtener más información sobre `CCCamera` puede encontrarse en el [documentos CocosSharp API](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/).

Los 100 píxeles mencionados anteriormente píxeles "juegos" en lugar de píxeles en el hardware. Esto significa que ejecutan el mismo juego en un dispositivo de una resolución diferente (por ejemplo, un iPad y iPhone) provocará en objetos que se coloca y el tamaño correcto con respecto a la pantalla física. En concreto, área visible del nuestro juego siempre será 1024 píxeles de alto y 768 píxeles de ancho, tal y como se trata de la resolución, hemos especificado anteriormente en el `LoadGame` método.


# <a name="adding-the-ball-ccsprite"></a>Agregar la CCSprite bola

Ahora que se ha familiarizado con los conceptos básicos sobre cómo trabajar con `CCSprite`, vamos a agregar el segundo `CCSprite` – una bola. Se dejarán ser siguiendo los pasos que son muy similares a cómo se crea la paleta `CCSprite`. 

En primer lugar, vamos a agregar la **ball.png** imagen desde la carpeta descomprimida en nuestro proyecto de iOS **contenido** carpeta. Seleccione esta opción para **copia** el archivo a nuestro **contenido** directory. Siga los mismos pasos anteriores para agregar un vínculo a la **ball.png** archivo en el proyecto de Android.

A continuación cree el `CCSprite` para este bola mediante la adición de un miembro denominado `ballSprite` a nuestro `GameScene` (clase), así como el código de creación de instancias para el `ballSprite`. Cuando termine nuestro `GameLayer` clase tendrá un aspecto similar al siguiente:


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


# <a name="adding-the-score-cclabel"></a>Agregar CCLabel puntuación

El último elemento visual que vamos a agregar a nuestra juego es un `CCLabel` para mostrar el número de veces que el usuario ha devuelto correctamente la bola. El `CCLabel` usa una fuente especificada en el constructor para las cadenas de presentación en pantalla.

Agregue el código siguiente para crear un `CCLabel` de instancia de `GameLayer`. Una vez terminado el código debe ser similar al siguiente:


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
    ...
```

Observe que está utilizando el scoreLabel una `AnchorPoint` de `CCPoint.AnchorUpperLeft`, lo que significa que la `PositionX` y `PositionY` valores definen su posición superior izquierda. Esto nos permite posición la `scoreLabel` con respecto a la parte superior izquierda de la pantalla sin necesidad de tener en cuenta las dimensiones de la etiqueta.


# <a name="implementing-every-frame-logic"></a>Implementa la lógica de cada fotograma

Hasta ahora, el juego presenta una escena estática. Iremos agregando lógica para controlar el movimiento de objetos de la escena agregando código que actualiza la posición de los objetos con una frecuencia alta. En este caso, el código se ejecutará sesenta veces por segundo - también se denomina sesenta *fotogramas* por segundo (a menos que el hardware no puede controlar actualizar con frecuencia, esto). En concreto, iremos agregando lógica para realizar la bola se dividen y rebote en la paleta, para mover la paleta según los datos proporcionados y para actualizar la puntuación del Reproductor cada vez que la bola real amenace la paleta.

El `Schedule` método, que se proporciona mediante la `CCNode` de clases, nos permite agregar lógica de cada marco a nuestro juego. Vamos a agregar el código después de `// New code` al constructor GameLayer:


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

Ahora cree una `RunGameLogic` método en nuestro `GameLayer` (clase), que contendrá toda nuestra lógica de cada marco de:


```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

El parámetro float define cuánto tiempo es nuestro marco en segundos. Usaremos este valor al implementar el movimiento de la bola.


## <a name="making-the-ball-fall"></a>Realizar la caída de la bola

Podemos hacer que la bola se dividen implementando manualmente nuestro propio código de gravedad o mediante la funcionalidad integrada de Box2D de CocosSharp. El motor de simulación de Box2D física está disponible para los juegos de CocosSharp. Es muy eficaz y eficiente, pero es necesario escribir código de programa de instalación. Puesto que nuestro simulación física es bastante simple, se implementará manualmente.

Para implementar la gravedad necesitamos almacén actual X y Y progreso de la bola. Vamos a agregar dos miembros a nuestro `GameLayer` clase:


```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

A continuación podemos implementar la lógica del tiempo de caída `RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

## <a name="moving-the-paddle-with-touch-input"></a>Mover la paleta con entrada táctil

Ahora que está acumulando la bola, vamos a agregar movimiento horizontal a nuestra paleta utilizando la `CCEventListenerTouchAllAtOnce` objeto. Este objeto proporciona una serie de eventos relacionados con la entrada. En nuestro caso, deseamos recibir una notificación si los puntos táctiles mueven por lo que podemos ajustar la posición de la paleta. El `GameLayer` ya crea una instancia de un `CCEventListenerTouchAllAtOnce`, por lo que necesitamos asignar el `OnTouchesMoved` delegar. Para ello, modifique el método AddedToScene de la siguiente manera:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of our drawable assets
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


## <a name="implementing-ball-collision"></a>Implementación de colisión de bola

Si se ejecuta el juego ahora se notará que la bola cae a través de la paleta. Implementaremos *colisión* (lógica para reaccionar a los objetos del juego) en el código de cada fotograma. Puesto que mover objetos modificados coloca cada marco, verificación de colisiones es normalmente también llevar a cabo cada fotograma. También agregaremos progreso en el eje X cuando la bola toca la paleta para agregar algunos desafío para el juego, pero esto significa que es necesario impedir que la bola mover más allá de los bordes de la pantalla. Deberá hacerlo el `RunGameLogic` código después de aplicar la velocidad a la `ballSprite` después `// New Code`:


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


## <a name="adding-scoring"></a>Adición de puntuación

Ahora que nuestro juego es reproducible, el último paso es agregar la lógica de puntuación. En primer lugar, vamos a agregar un miembro de puntuación para la clase GameLayer denominada `score`:


```csharp
int score;
```

Vamos a usar esta variable que se va a realizar un seguimiento de la puntuación del Reproductor como mostrarlo mediante nuestro `scoreLabel`. Para hacer esto agregue el código siguiente dentro de la instrucción if en `RunGameLogic` cuando se superponen las bolas y paleta:


```csharp
...
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
...
```

Ahora podemos ejecutar el juego y vea que nuestro juego muestra una puntuación que se incrementa según la bola rebota fuera de la paleta:

![](part2-images/image1.png "Ejecutar el juego y vea que muestra una puntuación que se incrementa según la bola rebota fuera de la paleta")


# <a name="summary"></a>Resumen

En este tutorial presenta la creación de un juego multiplataforma con gráficos, física e introducción de datos mediante CocosSharp. Es el primer paso en Introducción al desarrollo de juegos de CocosSharp. Hemos examinado algunas de las clases más comunes en CocosSharp, cómo construir un árbol visual para que los objetos se representan correctamente y cómo implementar la lógica de juego en cada fotograma.

Este tutorial se han tratado sólo una pequeña parte de lo que ofrece el motor de juegos de CocosSharp. Para obtener información y tutoriales en otros temas CocosSharp, consulte [el resto de nuestras guías CocosSharp](~/graphics-games/cocossharp/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Juego completado (ejemplo)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Contenido de juego (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
