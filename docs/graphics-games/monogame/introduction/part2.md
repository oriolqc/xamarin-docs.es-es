---
title: 'Parte 2: implementar la WalkingGame'
description: "Este tutorial muestra cómo agregar lógica de juego y el contenido a un proyecto vacío de MonoGame para crear una demostración de un sprite animado moviendo con entrada táctil."
ms.topic: article
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 2bc8f5448e249a801edfc88726f3dfb5d24b158d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2: implementar la WalkingGame

_Este tutorial muestra cómo agregar lógica de juego y el contenido a un proyecto vacío de MonoGame para crear una demostración de un sprite animado moviendo con entrada táctil._

Las partes anteriores de este tutorial se ha explicado cómo crear proyectos vacíos de MonoGame. Crearemos en estas partes anteriores mediante la realización de una simple demostración de juego. Este artículo contiene las siguientes secciones:

 - Descompresión nuestro contenido de juego
 - Información general de la clase MonoGame
 - Nuestro primer Sprite de representación
 - Crear el CharacterEntity
 - Agregar CharacterEntity al juego
 - Crear la clase de animación
 - Agregar la primera animación a CharacterEntity
 - Adición de movimiento para el carácter
 - Coincidencia de movimiento y animación


# <a name="unzipping-our-game-content"></a>Descompresión nuestro contenido de juegos

Antes de empezar a escribir código, queremos descomprima nuestro juego *contenido*. Los desarrolladores de juegos a menudo usa el término *contenido* para hacer referencia a los archivos de código que normalmente se crean mediante visuales intérpretes, diseñadores de juegos o diseñadores de audio. Tipos comunes de contenido son archivos que se utilizan para mostrar objetos visuales, reproducir un sonido o controlar el comportamiento de inteligencia artificial (AI). No son programadores normalmente crea contenido de perspectiva del equipo de desarrollo de juegos.

Puede encontrar el contenido que se usa aquí [en github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Necesitaremos estos archivos descargados en una ubicación que se tendrá acceso más adelante en este tutorial.


# <a name="monogame-class-overview"></a>Información general de la clase MonoGame

Para empezar a continuación se explican las clases de MonoGame utilizadas en representación básicas:

 - `SpriteBatch` : se utiliza para dibujar gráficos 2D a la pantalla. *Sprites* son 2D elementos visuales que se usan para mostrar imágenes en pantalla. El `SpriteBatch` objeto puede dibujar un sprite único a una hora comprendida entre su `Begin` y `End` métodos o varios objetos Sprite se puede agrupar, o *por lotes*.
 - `Texture2D` : representa un objeto de imagen en tiempo de ejecución. `Texture2D` a menudo se crean instancias de los formatos de archivo como archivo .png o .bmp, aunque también se pueden crear dinámicamente en tiempo de ejecución. `Texture2D` las instancias se utilizan al representar con `SpriteBatch` instancias.
 - `Vector2` : representa una posición en un sistema de coordenadas 2D que se suele utilizar para colocar los objetos visuales. También incluye MonoGame `Vector3` y `Vector4` , pero solo usaremos `Vector2` en este tutorial.
 - `Rectangle` : un área de cuatro puntas con la posición, ancho y alto. Usaremos esto para definir qué parte de nuestro `Texture2D` para representar cuando empezamos a trabajar con las animaciones.

También debemos mencionar que MonoGame no mantenido por Microsoft: a pesar de su espacio de nombres. El `Microsoft.Xna` espacio de nombres se utiliza en MonoGame para que resulten más fáciles de migrar los proyectos existentes de XNA a MonoGame.


# <a name="rendering-our-first-sprite"></a>Nuestro primer Sprite de representación

A continuación se dibujará un sprite único en la pantalla para mostrar cómo realizar la representación 2D en MonoGame.


## <a name="creating-a-texture2d"></a>Crear un Texture2D

Es necesario crear un `Texture2D` instancia a utilizar al representar el sprite. Todo el contenido de juego en última instancia se encuentra en una carpeta denominada **contenido,** ubicado en el proyecto específico de la plataforma. Proyectos de MonoGame compartido no pueden contener contenido, como el contenido debe utilizar acciones de compilación específica a la plataforma. Los desarrolladores de CocosSharp encontrará la carpeta de contenido un concepto familiar: se encuentran en el mismo lugar en los proyectos CocosSharp y MonoGame. La carpeta de contenido puede encontrarse en el proyecto de iOS y en la carpeta de activos en el proyecto de Android.

Para agregar contenido de nuestro del juego, haga doble clic en el **contenido** carpeta y seleccione **Agregar > Agregar archivos...** Navegue hasta la ubicación donde se extrajo el archivo content.zip y seleccione la **charactersheet.png** archivo. Si se le pregunta acerca de cómo agregar el archivo a la carpeta, debemos seleccionamos la **copia** opción:

![](part2-images/image1.png "Si se le pregunta acerca de cómo agregar el archivo a la carpeta, seleccione la opción de copia")

La carpeta de contenido contiene ahora el archivo charactersheet.png:

![](part2-images/image2.png "La carpeta de contenido contiene ahora el archivo de charactersheet.png")

A continuación, vamos a agregar código para cargar el archivo charactersheet.png y crear un `Texture2D`. Para ello, abra el `Game1.cs` de archivos y agregue el siguiente campo a la clase Game1.cs:


```csharp
Texture2D characterSheetTexture;
```

A continuación, vamos a crear la `characterSheetTexture` en el `LoadContent` método. Antes de las modificaciones `LoadContent` método tiene el siguiente aspecto:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```
Debemos mencionar que el proyecto predeterminado ya crea una instancia de la `spriteBatch` instancia para que podamos. Usaremos esto más adelante, pero se no puede agregar ningún código adicional para preparar la `spriteBatch` para su uso. Por otro lado, nuestro `spriteSheetTexture` requieren inicialización, por lo que se inicializará después de la `spriteBatch` se crea:


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Ahora que tenemos un `SpriteBatch` instancia y un `Texture2D` instancia podemos agregar el código de representación para el `Game1.Draw` método:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Ejecutar el juego ahora muestra un único sprite mostrar la textura creada a partir de charactersheet.png:

![](part2-images/image3.png "Ejecutar el juego ahora muestra un único sprite mostrar la textura creada a partir de charactersheet.png")


#  <a name="creating-the-characterentity"></a>Crear el CharacterEntity

Hasta ahora hemos agregado código para representar un único sprite a la pantalla; Sin embargo, si se llamase a desarrollar un juego completo sin necesidad de crear cualquier otra clase, se podría encontrarse con problemas de organización de código.


## <a name="what-is-an-entity"></a>¿Qué es una entidad?

Un patrón común para organizar el código de juego consiste en crear una nueva clase para cada juego *entidad* tipo. Una entidad en el desarrollo de juego es un objeto que puede contener algunas de las características siguientes (no todas son obligatorios):

 - Un elemento visual como un sprite, un texto o un modelo 3D
 - Física o cada comportamiento de marco, como una unidad patrolling una ruta de acceso del conjunto o un carácter de Reproductor responde de entrada
 - Se pueden crear y destruir de forma dinámica, como una puesta en marcha que aparecen y se recopilan por el Reproductor

Los sistemas de organización de entidad pueden ser complejos y muchos motores de juegos ofrecen clases que ayudan a administrar entidades. Se podrá implementar un sistema de entidad muy simple, por lo que debe tener en cuenta que los juegos completas suelen requieran más organizado por parte del desarrollador.


## <a name="defining-the-characterentity"></a>Definir la CharacterEntity

La entidad, que llamaremos `CharacterEntity`, tendrá las siguientes características:

 - La capacidad para cargar su propio `Texture2D`
 - La capacidad de representarse a sí misma, incluidos los que contiene métodos de llamada para actualizar la animación de recorrido
 - `X `y las propiedades de Y para controlar la posición del carácter.
 - La capacidad se actualice automáticamente, en concreto, para leer los valores del toque la pantalla y ajustar la posición de forma adecuada.

Para agregar el `CharacterEntity` a nuestro juego, menú contextual o Control, haga clic en el **WalkingGame** de proyecto y seleccione **Agregar > nuevo archivo...** . Seleccione el **clase vacía** opción y el nombre del nuevo archivo **CharacterEntity**, a continuación, haga clic en **nuevo**.

Primero vamos a agregar la posibilidad de que el `CharacterEntity` para cargar un `Texture2D` así como para dibujarse a sí mismo. Se modificará recién agregado `CharacterEntity.cs` como sigue:


```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

El código anterior agrega la responsabilidad de posición, representación y cargar contenido en el `CharacterEntity`. Dedique un momento para señalar algunas consideraciones realizadas en el código anterior.


## <a name="why-are-x-and-y-floats"></a>¿Por qué X y Y flotantes?

Los desarrolladores que trabajan con la programación de juegos quizás se pregunte por qué la `float` tipo se está usando en contraposición a `int` o `double`. La razón es que un valor de 32 bits es muy habitual para determinar la posición en el código de representación de bajo nivel. El tipo double ocupa de 64 bits de precisión, que rara vez es necesario para la colocación de objetos. Mientras una diferencia de 32 bits puede parecer insignificante, muchos juegos modernos incluirán gráficos que requieren el procesamiento decenas de miles de valores de posición cada fotograma (30 o 60 veces por segundo). Cortar la cantidad de memoria que se debe mover a través de los gráficos canalización a la mitad puede tener un impacto significativo en el rendimiento del juego.

El `int` tipo de datos puede ser una unidad de medida para determinar la posición adecuada, pero pueden colocar las limitaciones en la forma en que se colocan las entidades. Por ejemplo, usando valores enteros hace más difícil implementar movimiento suave para juegos que admite la opción de acercar o cámaras 3D (que se admiten en `SpriteBatch`).

Por último, veremos que la lógica que se mueve el carácter por la pantalla lo hará con los valores de tiempo del juego. Rara vez se producirá el resultado de multiplicar velocidad por cuánto tiempo ha pasado en un periodo especificado en un número entero, por lo que debemos usar `float` para `X` y `Y`.


## <a name="why-is-charactersheettexture-static"></a>¿Por qué es characterSheetTexture estático?

El `characterSheetTexture` `Texture2D` instancia es una representación en tiempo de ejecución del archivo charactersheet.png. En otras palabras, contiene los valores de color para cada píxel tal como se extrae el origen **charactersheet.png** archivo. Si el juego crease dos `CharacterEntity` instancias, a continuación, cada uno de ellos tendría acceso a la información de charactersheet.png. En esta situación no queremos incurrir en el costo de rendimiento de la carga charactersheet.png dos veces, ni se desearía tener memoria de textura duplicados almacenado en la memoria ram. Con un `static` campo nos permite compartir el mismo `Texture2D` en todas las `CharacterEntity` instancias.

Usar `static` miembros para la representación en tiempo de ejecución de contenido es una solución simplista y no soluciona los problemas encontrados en juegos más grandes, como descargar contenido cuando se mueve de un nivel a otro. Más soluciones sofisticadas, que están fuera del ámbito de este tutorial, incluyen mediante canalización contenido del MonoGame o crear un sistema de administración de contenido personalizado.


## <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>¿Por qué es SpriteBatch pasa como un argumento Instead of crea una instancia de la entidad?

El `Draw` método tal como se implementó anteriormente toma un `SpriteBatch` argumento, pero en cambio, el `characterSheetTexture` se crea una instancia por la `CharacterEntity`.

La razón para esto es porque la representación más eficaz posible cuando el mismo `SpriteBatch` instancia se utiliza para todos los `Draw` llamadas y cuando todos los `Draw` se realizan las llamadas entre un conjunto único de `Begin` y `End` llamadas. Por supuesto, nuestro juego solo incluirá una única instancia, pero más complicados juegos se beneficiarán de patrón que permite que varias entidades que se usan los mismos `SpriteBatch` instancia.


# <a name="adding-characterentity-to-the-game"></a>Agregar CharacterEntity al juego

Ahora que hemos agregado nuestro `CharacterEntity` con código de representación propia, podemos reemplazar el código en `Game1.cs` para utilizar una instancia de esta nueva entidad. Para ello, vamos a reemplazar el `Texture2D` campo con un `CharacterEntity` campo `Game1`:


```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

A continuación, agregaremos la creación de instancias de esta entidad en `Game1.Initialize`:


```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

También es necesario quitar el `Texture2D` creación de `LoadContent` desde que lo controla ahora dentro de nuestro `CharacterEntity`. `Game1.LoadContent` debe tener un aspecto similar al siguiente:


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Merece la pena mencionar que a pesar de su nombre de la `LoadContent` método no es el único lugar donde se puede cargar contenido: muchas implemente juegos contenido cargar en su método de inicialización, o incluso en el código de actualización como contenido, no es necesario hasta que el Reproductor alcanza un punto determinado del juego.

Por último, podemos modificamos el método Draw como sigue:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Si se ejecuta el juego, se verán el carácter. Puesto que X e Y como valor predeterminado 0, el carácter se coloca en la esquina superior izquierda de la pantalla:

![](part2-images/image4.png "Puesto que X e Y como valor predeterminado 0, el carácter se coloca en la esquina superior izquierda de la pantalla")


# <a name="creating-the-animation-class"></a>Crear la clase de animación

Actualmente nuestro `CharacterEntity` muestra toda la **charactersheet.png** archivo. Esta organización de varias imágenes en un archivo se conoce como un *hoja sprite*. Normalmente, un objeto sprite representará solo una parte de la hoja de sprite. Se modificará la `CharacterEntity` para representar una parte de este **charactersheet.png**, y esta parte cambiará con el tiempo para mostrar una animación de recorrido.

Se creará el `Animation` clase para controlar la lógica y el estado de la animación CharacterEntity. La clase de animación será una clase general que se pueda usar para cualquier entidad, no solo `CharacterEntity` animaciones. Ultimate el `Animation` clase proporcionará un `Rectangle` que la `CharacterEntity` va a usar al dibujar a sí mismo. También crearemos un `AnimationFrame` clase que se utilizará para definir la animación.


## <a name="defining-animationframe"></a>Definir AnimationFrame

`AnimationFrame` no contendrá ninguna lógica relacionada con la animación. Lo usaremos solo para almacenar los datos. Para agregar el `AnimationFrame` class, menú contextual o Control, haga clic en el **WalkingGame** compartido proyecto y seleccione **Agregar > nuevo archivo...** Escriba el nombre **AnimationFrame** y haga clic en el **New** botón. Se modificará la `AnimationFrame.cs` de archivos para que contenga el código siguiente:


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

La `AnimationFrame` clase contiene dos piezas de información:

 - `SourceRectangle` : Define el área de la `Texture2D` que mostrará el `AnimationFrame`. Este valor se mide en píxeles, con los superior izquierdo (0, 0).
 - `Duration` : Define cuánto tiempo un `AnimationFrame` se muestra cuando se utiliza en un `Animation`.


## <a name="defining-animation"></a>Definición de animación

El `Animation` clase contendrá un `List<AnimationFrame>` , así como la lógica para cambiar el marco al que actualmente se muestra según la cantidad de tiempo transcurrido.

Para agregar el `Animation` class, menú contextual o Control, haga clic en el **WalkingGame** compartido proyecto y seleccione **Agregar > nuevo archivo...** . Escriba el nombre **animación** y haga clic en el **New** botón. Se modificará la `Animation.cs` archivo así que contiene el código siguiente:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Echemos un vistazo a algunos de los detalles de la `Animation` clase.


## <a name="frames-list"></a>Lista de marcos

El `frames` miembro es lo que almacena los datos para la animación. Se agregará el código que crea instancias de las animaciones `AnimationFrame` instancias para el `frames` lista a través de la `AddFrame` método. Puede ofrecer una implementación más completa `public` métodos o propiedades para modificar `frames`, pero se podrá limitar la funcionalidad para agregar fotogramas para este tutorial.


## <a name="duration"></a>Duración

Duración devuelve la duración total de la `Animation,` que se obtiene mediante la adición de la duración de todos los contenidos `AnimationFrame` instancias. Este valor puede estar en la caché si `AnimationFrame` tratara de un objeto inmutable, pero puesto que AnimationFrame se implementa como una clase que se puede cambiar después de que se va a agregar a la animación, necesitamos para calcular este valor cada vez que se tiene acceso a la propiedad.


## <a name="update"></a>Actualizar

El `Update` método debe llamarse cada marco (es decir, cada vez que se actualiza toda la partida). Su objetivo es aumentar la `timeIntoAnimation` miembro que se utiliza para devolver el fotograma mostrado actualmente. La lógica de `Update` evita la `timeIntoAnimation` nunca sea mayor que la duración de toda la animación.

Puesto que usaremos el `Animation` clase para mostrar una animación de recorrido, a continuación, queremos que esta animación se repita cuando ha llegado al final. Podemos lograr esto mediante la comprobación de si el `timeIntoAnimation` es mayor que el `Duration` valor. Caso en ese se desplazarse hasta el principio y conservar el resto, lo que da lugar a una animación en bucle.


## <a name="obtaining-the-current-frames-rectangle"></a>Obtener el rectángulo del fotograma actual

El propósito de la `Animation` clase es el usuario quien proporcionar un `Rectangle` que se puede usar al dibujar un sprite. Actualmente el `Animation` clase contiene la lógica para cambiar la `timeIntoAnimation` miembro, que se usará para obtener que `Rectangle` debe mostrarse. Deberá hacerlo mediante la creación de un `CurrentRectangle` propiedad en la `Animation` clase. Copie esta propiedad en la `Animation` clase:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime;
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

# <a name="adding-the-first-animation-to-characterentity"></a>Agregar la primera animación a CharacterEntity

El `CharacterEntity` contendrá las animaciones para recorrer y permanentes, así como una referencia a la actual `Animation` mostrarse.

En primer lugar, vamos a agregar nuestro primer `Animation,` que vamos a usar para probar la funcionalidad tal y como se han escrito. Vamos a agregar a los miembros siguientes a la clase CharacterEntity:


```csharp
Animation walkDown;
Animation currentAnimation;
```

 A continuación, vamos a definir la `walkDown` animación. Para hacer esto modificar el `CharacterEntity` constructor como se indica a continuación:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```
Como se mencionó anteriormente, es necesario llamar a `Animation.Update` para animaciones basadas en tiempo jugar. También es necesario asignar la `currentAnimation`. Para que ahora se asignará el `currentAnimation` a `walkDown`, pero se va a sustituir este código más adelante cuando se implementa la lógica de movimiento. Vamos a agregar la `Update` método `CharacterEntity` como se indica a continuación:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Ahora que tenemos la `currentAnimation` que se va a asignar y actualizado, se puede usar para realizar el dibujo. Se modificará `CharacterEntity.Draw` como se indica a continuación:


```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

El último paso para obtener la `CharacterEntity` animar consiste en llamar a recién agregado `Update` método desde `Game1`. Modificar `Game1.Update` como se indica a continuación:


```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Ahora el `CharacterEntity` desempeñará su `walkDown` animación:

![](part2-images/image5.gif "Ahora el CharacterEntity reproducirá la animación walkDown")


# <a name="adding-movement-to-the-character"></a>Adición de movimiento para el carácter

A continuación, iremos agregando movimiento a nuestro carácter mediante controles de entrada táctil. Cuando el usuario toca la pantalla, el carácter se moverá hacia el punto donde se toca la pantalla. Si no se detecta ningún un toque, el carácter se mostrará en su lugar.


## <a name="defining-getdesiredvelocityfrominput"></a>Definir GetDesiredVelocityFromInput

Usaremos del MonoGame `TouchPanel` (clase), que proporciona información sobre el estado actual de la pantalla táctil. Vamos a agregar un método que comprobará la `TouchPanel` y devolver la velocidad deseada de nuestro carácter:


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

El `TouchPanel.GetState` método devuelve un `TouchCollection` que contiene información sobre donde el usuario toca la pantalla. Si el usuario no es tocar la pantalla, la `TouchCollection` estará vacío, en cuyo caso no deberíamos pasamos el carácter.

Si el usuario toca la pantalla, se moverá el carácter hacia el primer toque, en otras palabras, el `TouchLocation` en el índice 0. Inicialmente, configuraremos la velocidad deseada para igualar la diferencia entre la ubicación del carácter y la ubicación del primer toque:


```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

La información siguiente es un poco de matemáticas que mantendrá el carácter mover a la misma velocidad. Para ayudar a explicar por qué esto es importante, consideremos una situación donde el usuario toca el 500 píxeles de pantalla lejos de donde está ubicado el carácter. La primera línea where `desiredVelocity.X` es conjunto podría asignar un valor de 500. Sin embargo, si el usuario toca la pantalla a una distancia de solo 100 unidades de carácter, el `desiredVelocity.X `se establecería en 100. El resultado sería que velocidad de movimiento del carácter respondería cómo lejos que esté el punto de entrada táctil el carácter. Puesto que deseamos que el carácter que se va a mover siempre a la misma velocidad, es necesario modificar el desiredVelocity.

El `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instrucción está comprobando si la velocidad es distinta de cero: en otras palabras, se comprueba para asegurarse de que el usuario no usa la misma posición como la posición del carácter actual. Si no es así, a continuación, es necesario establecer velocidad del carácter sea constante, independientemente de cómo lejos toque es. Esto se consigue con la normalización del vector de velocidad que da como resultado que sea una longitud de 1. Un vector de velocidad de 1 significa que el carácter se moverá en 1 píxel por segundo. Esto se podrá acelerar el multiplicando el valor por la velocidad deseada de 200.


## <a name="applying-velocity-to-position"></a>Aplicación de progreso a posición

El progreso devuelto desde `GetDesiredVelocityFromInput` debe aplicarse al carácter `X` y `Y` valores tener ningún efecto en tiempo de ejecución. Se modificará la `Update` método tal como se indica a continuación:


```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Se llama a lo que hemos implementado aquí *basado en tiempo* movimiento (en contraposición a *basados en marcos* movimiento). Movimiento, basándose en el tiempo multiplica un valor de velocidad (en nuestro caso los valores almacenados en el `velocity` variable) por cuánto tiempo ha transcurrido desde la última actualización que se almacena en `gameTime.ElapsedGameTime.TotalSeconds`. Si el juego se ejecuta en menos fotogramas por segundo, aumenta el tiempo transcurrido entre marcos: el resultado final es que los objetos mediante el movimiento, basándose en el tiempo siempre se moverán a la misma velocidad, independientemente de la velocidad de fotogramas.

Si ejecutamos nuestro juego ahora, veremos que el carácter está avanzando hacia la ubicación de entrada táctil:

![](part2-images/image6.gif "El carácter se mueve hacia la ubicación de entrada táctil")


# <a name="matching-movement-and-animation"></a>Coincidencia de movimiento y animación

Una vez que tenemos nuestro carácter mover y la reproducción de una animación, podemos definir el resto de nuestro animaciones, y usarlos para reflejar el movimiento del carácter. Cuando termine de tenemos ocho animaciones en total:

 - Animaciones para recorrer hacia arriba, abajo, izquierda y derecha
 - Las animaciones elegibles todavía y hacia arriba, abajo, izquierda y derecha


## <a name="defining-the-rest-of-the-animations"></a>Definir el resto de las animaciones

En primer lugar, vamos a agregar la `Animation` instancias para el `CharacterEntity` clase todos nuestras animaciones en el mismo lugar donde se agrega `walkDown`. Después de hacer esto, el `CharacterEntity` tendrá el siguiente `Animation` miembros:


```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Ahora se definirá las animaciones en el `CharacterEntity` constructor como se indica a continuación:


```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Debemos mencionar que el código anterior se agregó a la `CharacterEntity` constructor para mantener este tutorial más corto. Normalmente los juegos se separar la definición de animaciones de carácter en sus propias clases o cargar esta información desde un formato de datos como XML o JSON.

A continuación, se podrá ajustar la lógica para usar las animaciones de acuerdo con la dirección que está moviendo el carácter, o de acuerdo con la última animación si el carácter solo se ha detenido. Para ello, se modificará el `Update` método:


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

El código para cambiar las animaciones se divide en dos bloques. La primera comprueba si no es igual a la velocidad `Vector2.Zero` : Esto nos indica si el carácter es móvil. Si el carácter que se está moviendo, podemos observar la `velocity.X` y `velocity.Y` valores para determinar qué recorrido animación para reproducir.

Si el carácter no se mueve, queremos establecer el carácter `currentAnimation` a una animación permanentes: pero haremos lo solo si la `currentAnimation` es un recorrido de la animación o si no se ha establecido una animación. Si el `currentAnimation` no es uno de las cuatro animaciones recorridas, a continuación, el carácter ya es permanente por lo que no es necesario cambiar `currentAnimation`.

El resultado de este código es que el carácter se animar correctamente cuando se lleva y, a continuación, se enfrentan a la dirección del último que se ha recorrido cuando se detiene:

![](part2-images/image7.gif "El resultado de este código es que el carácter se animar correctamente cuando se lleva y, a continuación, se enfrentan a la dirección del último que se ha recorrido cuando se detiene")


# <a name="summary"></a>Resumen

En este tutorial se ha mostrado cómo trabajar con MonoGame para crear soluciones multiplataforma juego con sprites, movimiento de objetos, la detección de entrada y la animación. Se ocupa de crear una clase de animación de uso general. También ha mostrado cómo crear una entidad de caracteres para organizar la lógica del código.

## <a name="related-links"></a>Vínculos relacionados

- [Recurso de imagen CharacterSheet (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Recorrido completo de juego (ejemplo)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
