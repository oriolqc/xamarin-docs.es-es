---
title: 'Parte 2: implementación de WalkingGame'
description: Este tutorial muestra cómo agregar la lógica del juego y el contenido a un proyecto MonoGame vacío para crear una demostración de un sprite animado mover con entrada táctil.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 941b88f9109cf2f3a3485311c52b1250bd08e53f
ms.sourcegitcommit: 2ee36611ef667affee7d417db947fbb614d75315
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479776"
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2: implementación de WalkingGame

_Este tutorial muestra cómo agregar la lógica del juego y el contenido a un proyecto MonoGame vacío para crear una demostración de un sprite animado mover con entrada táctil._

Las partes anteriores de este tutorial se ha explicado cómo crear proyectos vacíos de MonoGame. Vamos a crear en estas partes anteriores mediante la realización de una simple demostración del juego. Este artículo contiene las siguientes secciones:

- Al descomprimir el contenido de juegos
- Información general de clases de MonoGame
- Nuestro primer Sprite de representación
- Crear el CharacterEntity
- Agregar CharacterEntity del juego
- Creación de la clase de animación
- Agregar la primera animación a CharacterEntity
- Adición de movimiento al carácter
- Coincidencia de movimiento y la animación


## <a name="unzipping-our-game-content"></a>Al descomprimir el contenido de juegos

Antes de empezar a escribir código, queremos descomprima nuestro juego *contenido*. Los desarrolladores de juegos a menudo usan el término *contenido* para hacer referencia a archivos de código que normalmente son creados por visuales artistas, diseñadores de juego o diseñadores de audio. Tipos de contenido comunes incluyen los archivos que se usa para mostrar los objetos visuales, reproducir un sonido o controlar el comportamiento de inteligencia artificial (IA). No son programadores normalmente crea contenido de perspectiva del equipo de desarrollo de juegos.

Puede encontrar el contenido que se usa aquí [en github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Necesitaremos estos archivos descargados en una ubicación que se tendrá acceso a más adelante en este tutorial.

## <a name="monogame-class-overview"></a>Información general de clases de MonoGame

Para empezar, exploraremos las clases de MonoGame utilizadas en representación básica:

- `SpriteBatch` : se utiliza para dibujar gráficos en 2D a la pantalla. *Sprites* son elementos visuales 2D que sirven para mostrar imágenes en pantalla. El `SpriteBatch` objeto puede dibujar un sprite único cada vez entre su `Begin` y `End` métodos o varios objetos Sprite se puede agrupar, o *por lotes*.
- `Texture2D` : representa un objeto de imagen en tiempo de ejecución. `Texture2D` a menudo se crean instancias de los formatos de archivo como archivo .png o .bmp, aunque también se pueden crear dinámicamente en tiempo de ejecución. `Texture2D` las instancias se utilizan al representar con `SpriteBatch` instancias.
- `Vector2` : representa una posición en un sistema de coordenadas 2D que a menudo se usa para colocar los objetos visuales. También incluye MonoGame `Vector3` y `Vector4` , pero solo se usará `Vector2` en este tutorial.
- `Rectangle` : un área de cuatro lados con posición, ancho y alto. Vamos a usar esto para definir qué parte de nuestro `Texture2D` para representar cuando empezamos a trabajar con las animaciones.

También debemos mencionar que MonoGame no mantenido por Microsoft: a pesar de su espacio de nombres. El `Microsoft.Xna` espacio de nombres se utiliza en MonoGame para facilitar la migración de proyectos existentes de XNA a MonoGame.

## <a name="rendering-our-first-sprite"></a>Nuestro primer Sprite de representación

A continuación se dibujará un sprite único en la pantalla para mostrar cómo realizar la representación 2D en MonoGame.

### <a name="creating-a-texture2d"></a>Creación de una Texture2D

Es necesario crear un `Texture2D` instancia que se utiliza al representar el sprite. Todo el contenido juegos en última instancia se encuentra en una carpeta denominada **contenido,** ubicado en el proyecto específico de la plataforma. Proyectos MonoGame compartido no pueden contener el contenido, como el contenido debe utilizar acciones de compilación específicas de la plataforma. Los desarrolladores de CocosSharp encontrará la carpeta de contenido un concepto familiar: están ubicados en el mismo lugar en los proyectos de CocosSharp y MonoGame. La carpeta de contenido puede encontrarse en el proyecto de iOS y en la carpeta activos en el proyecto de Android.

Para agregar contenido de nuestro juego, haga doble clic en el **contenido** carpeta y seleccione **Agregar > Agregar archivos...** Vaya a la ubicación donde se extrajo el archivo content.zip y seleccione el **charactersheet.png** archivo. Si se le pregunta acerca de cómo agregar el archivo a la carpeta, debemos seleccionamos la **copia** opción:

![](part2-images/image1.png "Si se le pregunta acerca de cómo agregar el archivo a la carpeta, seleccione la opción de copia")

La carpeta de contenido contiene el archivo charactersheet.png:

![](part2-images/image2.png "La carpeta de contenido contiene el archivo charactersheet.png")

A continuación, agregaremos código para cargar el archivo charactersheet.png y crear un `Texture2D`. Para ello, abra el `Game1.cs` archivo y agregue el siguiente campo a la clase Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

A continuación, vamos a crear la `characterSheetTexture` en el `LoadContent` método. Antes de realizar modificaciones `LoadContent` método tiene este aspecto:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Debemos mencionar que el proyecto predeterminado ya crea una instancia de la `spriteBatch` instancia para nosotros. Vamos a usar esto más adelante, pero que no van a agregar ningún código adicional para preparar la `spriteBatch` para su uso. Por otro lado, nuestro `spriteSheetTexture` requieren inicialización, por lo que se inicializará después el `spriteBatch` se crea:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Ahora que tenemos un `SpriteBatch` instancia y un `Texture2D` instancia podemos agregar nuestro código de representación para el `Game1.Draw` método:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Ejecutar el juego ahora muestra un sprite solo mostrar la textura creada a partir de charactersheet.png:

![](part2-images/image3.png "Ejecutar el juego ahora muestra un sprite solo mostrar la textura creada a partir de charactersheet.png")

## <a name="creating-the-characterentity"></a>Crear el CharacterEntity

Hasta ahora hemos agregado código para representar un sprite único a la pantalla; Sin embargo, si tuviéramos que desarrollar un juego completo sin necesidad de crear otras clases, se ejecutaría en problemas de organización de código.

### <a name="what-is-an-entity"></a>¿Qué es una entidad?

Un patrón común para organizar el código de juego consiste en crear una nueva clase para cada juego *entidad* tipo. Una entidad en el desarrollo de juegos es un objeto que puede contener algunas de las características siguientes (no todos son obligatorios):

- Un elemento visual como un sprite, texto o un modelo 3D
- Física o cada comportamiento del marco, como una unidad patrolling una ruta de acceso del conjunto o un carácter de Reproductor responde de entrada
- Se pueden crear y destruir de forma dinámica, como una alimentación vertical que aparece y se recopilan mediante el Reproductor

Sistemas de la organización de la entidad pueden ser complejos, y muchos motores de juegos ofrecen clases que ayudan a administrar las entidades. Se deberá implementar un sistema de entidad muy sencillo, por lo que conviene tener en cuenta que los juegos completas suelen requieran más organización por parte del desarrollador.


### <a name="defining-the-characterentity"></a>Definir la CharacterEntity

La entidad, que llamaremos `CharacterEntity`, tendrá las siguientes características:

- La capacidad de cargar su propio `Texture2D`
- La capacidad de representarse a sí misma, como que contiene métodos de llamada para actualizar la animación pasear
- `X` propiedades e Y para controlar la posición del carácter.
- La capacidad de actualizarse automáticamente, en concreto, para leer los valores del toque la pantalla y ajustan la posición de forma adecuada.

Para agregar la `CharacterEntity` a nuestro juego, haga clic o pulse Control en el **WalkingGame** del proyecto y seleccione **Agregar > nuevo archivo...** . Seleccione el **clase vacía** opción y asigne al nuevo archivo **CharacterEntity**, a continuación, haga clic en **New**.

Primero vamos a agregar la posibilidad de que el `CharacterEntity` para cargar un `Texture2D` así como para dibujarse a sí mismo. Modificaremos recién agregado `CharacterEntity.cs` archivo como sigue:

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

El código anterior agrega la responsabilidad de posicionamiento, representación y cargar contenido en el `CharacterEntity`. Dedique un momento para señalar algunas consideraciones realizadas en el código anterior.

### <a name="why-are-x-and-y-floats"></a>¿Por qué X y Y flotantes?

Los desarrolladores sin experiencia en programación de juegos quizás se pregunte por qué la `float` tipo se está usando en contraposición a `int` o `double`. El motivo es que un valor de 32 bits es más común para la colocación en el código de representación de bajo nivel. El tipo double ocupa de 64 bits de precisión, que rara vez es necesario para colocar objetos. Una diferencia de 32 bits puede parecer insignificante, mientras que muchos de los juegos modernos incluyen gráficos que requieren procesamiento decenas de miles de valores de posición de cada fotograma (30 o 60 veces por segundo). Cortar la cantidad de memoria que se debe mover a través de los gráficos canalización a la mitad puede tener un impacto significativo en el rendimiento de un juego.

El `int` tipo de datos puede ser una unidad de medida para el posicionamiento adecuada, pero pueden colocar las limitaciones en la forma en que se colocan las entidades. Por ejemplo, usando valores enteros resulta más difícil de implementar un movimiento suave para juegos que admiten acercar, o cámaras 3D (que están permitidas por `SpriteBatch`).

Por último, veremos que la lógica que mover el carácter en torno a la pantalla lo hará con los valores de tiempo del juego. El resultado de multiplicar velocidad por cuánto tiempo ha pasado de un fotograma determinado rara vez se producirá en un número entero, por lo que necesitamos usar `float` para `X` y `Y`.

### <a name="why-is-charactersheettexture-static"></a>¿Por qué es characterSheetTexture estático?

El `characterSheetTexture` `Texture2D` instancia es una representación en tiempo de ejecución del archivo charactersheet.png. En otras palabras, contiene los valores de color para cada píxel como extraídos del origen de **charactersheet.png** archivo. Si nuestro juego tuviera que crear dos `CharacterEntity` instancias, a continuación, cada uno de ellos debe poder acceder a la información de charactersheet.png. En esta situación no desea incurrir en el costo de rendimiento de la carga charactersheet.png dos veces, ni íbamos a desear tener memoria de textura duplicados almacena en ram. Mediante un `static` campo nos permite compartir el mismo `Texture2D` todas `CharacterEntity` instancias.

Uso de `static` los miembros para la representación en tiempo de ejecución de contenido es una solución simplista y no soluciona problemas encontrados en juegos más grandes, como descargar contenido al pasar de un nivel a otro. Las soluciones más sofisticadas, que están fuera del ámbito de este tutorial, incluyen el uso de canalización de contenido de MonoGame o creación de un sistema de administración de contenido personalizado.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>¿Por qué es SpriteBatch pasa como un argumento en lugar de crear instancias de la entidad?

El `Draw` método tal como está implementado anteriormente toma un `SpriteBatch` argumento, pero por el contrario, el `characterSheetTexture` se crea una instancia por el `CharacterEntity`.

La razón de esto es porque la representación más eficaz es posible cuando el mismo `SpriteBatch` instancia se utiliza para todos `Draw` llamadas y cuando todos los `Draw` se están realizando llamadas entre un conjunto único de `Begin` y `End` llamadas. Por supuesto, nuestro juego solo incluirá una única instancia, pero más complicados juegos se beneficiarán de patrón que permite que varias entidades usar el mismo `SpriteBatch` instancia.


## <a name="adding-characterentity-to-the-game"></a>Agregar CharacterEntity del juego

Ahora que hemos agregado nuestro `CharacterEntity` con el código para representar a sí misma, podemos reemplazar el código en `Game1.cs` para utilizar una instancia de esta nueva entidad. Para ello, vamos a reemplazar el `Texture2D` campo con un `CharacterEntity` campo `Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

A continuación, agregaremos la creación de instancias de esta entidad en `Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

También es necesario quitar el `Texture2D` creación desde `LoadContent` ya que ahora que se controlan dentro de nuestro `CharacterEntity`. `Game1.LoadContent` un aspecto similar al siguiente:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Es importante destacar que, a pesar de su nombre el `LoadContent` método no es el único lugar donde se puede cargar contenido: muchos implemente juegos contenido cargar en su método Initialize, o incluso en su código de actualización como contenido, puede no ser necesario hasta que el jugador alcanza un punto determinado del juego.

Por último, podemos modificar el método Draw como sigue:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Si se ejecuta el juego, veremos ahora el carácter. Dado que X e Y como valor predeterminado 0, el carácter se coloca en la esquina superior izquierda de la pantalla:

![](part2-images/image4.png "Dado que X e Y como valor predeterminado 0, el carácter se coloca en la esquina superior izquierda de la pantalla")

## <a name="creating-the-animation-class"></a>Creación de la clase de animación

Actualmente nuestro `CharacterEntity` muestra el completo **charactersheet.png** archivo. Esta disposición de varias imágenes en un archivo se denomina un *hoja de sprites*. Normalmente, un sprite representará sólo una parte de la hoja de sprites. Modificaremos el `CharacterEntity` para representar una parte de este **charactersheet.png**, y esta parte cambiará con el tiempo para mostrar una animación pasear.

Se creará el `Animation` clase para controlar la lógica y estado de la animación CharacterEntity. La clase de animación será una clase general que podría usarse para cualquier entidad, no solo `CharacterEntity` animaciones. Ultimate el `Animation` clase proporcionará un `Rectangle` que el `CharacterEntity` va a usar al dibujar a sí mismo. También crearemos un `AnimationFrame` clase que se usará para definir la animación.


### <a name="defining-animationframe"></a>Definir AnimationFrame

`AnimationFrame` no contendrá ninguna lógica relacionada con la animación. Vamos a usar, solo para almacenar los datos. Para agregar la `AnimationFrame` clase, haga clic o pulse Control en el **WalkingGame** comparten el proyecto y seleccione **Agregar > nuevo archivo...** Escriba el nombre **AnimationFrame** y haga clic en el **New** botón. Modificaremos el `AnimationFrame.cs` de archivos para que contenga el código siguiente:


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

La `AnimationFrame` clase contiene dos fragmentos de información:

- `SourceRectangle` : Define el área de la `Texture2D` que será mostrado por el `AnimationFrame`. Este valor se mide en píxeles, siendo el superior izquierdo (0, 0).
- `Duration` : Define cuánto tiempo un `AnimationFrame` se muestra cuando se utiliza en un `Animation`.

### <a name="defining-animation"></a>Definición de animación

El `Animation` clase contendrá un `List<AnimationFrame>` , así como la lógica para cambiar el marco al que se muestra actualmente según cuánto tiempo ha pasado.

Para agregar la `Animation` clase, haga clic o pulse Control en el **WalkingGame** comparten el proyecto y seleccione **Agregar > nuevo archivo...** . Escriba el nombre **animación** y haga clic en el **New** botón. Modificaremos el `Animation.cs` archivo así que contiene el código siguiente:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Echemos un vistazo a algunos de los detalles de la `Animation` clase.

### <a name="frames-list"></a>Lista de fotogramas

El `frames` miembro es lo que almacena los datos para la animación. Se agregará el código que crea una instancia de las animaciones `AnimationFrame` instancias para el `frames` enumerar a través de la `AddFrame` método. Puede ofrecer una implementación más completa `public` métodos o propiedades para modificar `frames`, pero se le limita la funcionalidad a la adición de marcos para este tutorial.

### <a name="duration"></a>Duración

Duración devuelve la duración total de la `Animation,` que se obtiene mediante la adición de la duración de todos los contenidos `AnimationFrame` instancias. Este valor podría almacenarse en caché si `AnimationFrame` fuera un objeto inmutable, pero dado que AnimationFrame se implementa como una clase que se puede cambiar después de agregarse a la animación, necesitamos para calcular este valor cada vez que se tiene acceso a la propiedad.

### <a name="update"></a>Actualizar

El `Update` método debe llamarse cada fotograma (es decir, cada vez que se actualiza toda la partida). Su objetivo es aumentar la `timeIntoAnimation` miembro que se usa para devolver el fotograma mostrado actualmente. La lógica de `Update` evita la `timeIntoAnimation` nunca se supera la duración de la animación completa.

Puesto que vamos a usar la `Animation` clase para mostrar una animación pasear entonces queremos tener esta animación se repiten cuando ha llegado al final. Podemos lograrlo mediante la comprobación de si el `timeIntoAnimation` es mayor que el `Duration` valor. En caso afirmativo se vuelve al principio del ciclo y conservar el resto, lo que resulta en una animación de bucle.

### <a name="obtaining-the-current-frames-rectangle"></a>Obtener el rectángulo del marco actual

El propósito de la `Animation` clase es en última instancia proporcionar un `Rectangle` que se puede usar al dibujar un sprite. Actualmente la `Animation` clase contiene la lógica para cambiar la `timeIntoAnimation` miembro, lo que vamos a usar para obtener que `Rectangle` debe mostrarse. Haremos esto mediante la creación de un `CurrentRectangle` propiedad en el `Animation` clase. Copie esta propiedad en el `Animation` clase:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Agregar la primera animación a CharacterEntity

El `CharacterEntity` contendrá las animaciones para recorrer y permanente, así como una referencia a la actual `Animation` que se muestran.

En primer lugar, vamos a agregar nuestro primer `Animation,` que vamos a usar para probar las funciones como escrito hasta ahora. Vamos a agregar a los miembros siguientes a la clase CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

A continuación, vamos a definir el `walkDown` animación. Hacer esto modificar el `CharacterEntity` constructor como sigue:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Como se mencionó anteriormente, es necesario llamar a `Animation.Update` para animaciones basadas en tiempo jugar. También necesitamos asignar el `currentAnimation`. Por ahora, le asignamos el `currentAnimation` a `walkDown`, pero que se va a sustituir este código más adelante cuando se implementa la lógica de movimiento. Vamos a agregar el `Update` método `CharacterEntity` como sigue:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Ahora que tenemos la `currentAnimation` está asignado y actualizado, se puede usar para realizar el dibujo. Modificaremos `CharacterEntity.Draw` como sigue:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

El último paso para certificar la `CharacterEntity` animar consiste en llamar a recién agregada `Update` método desde `Game1`. Modificar `Game1.Update` como sigue:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Ahora el `CharacterEntity` se reproducirá su `walkDown` animación:

![](part2-images/image5.gif "Ahora el CharacterEntity reproducirá la animación walkDown")

## <a name="adding-movement-to-the-character"></a>Adición de movimiento al carácter

A continuación, agregaremos movimiento en nuestra carácter usando los controles táctiles. Cuando el usuario toca la pantalla, el carácter se moverá hacia el punto donde se toca la pantalla. Si no se detectan retoques, el carácter se mantendrán en su lugar.

### <a name="defining-getdesiredvelocityfrominput"></a>Definir GetDesiredVelocityFromInput

Vamos a usar de MonoGame `TouchPanel` (clase), que proporciona información sobre el estado actual de la pantalla táctil. Vamos a agregar un método que comprobará el `TouchPanel` y devolver la velocidad de nuestro del carácter deseado:


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

El `TouchPanel.GetState` método devuelve un `TouchCollection` que contiene información sobre donde el usuario toca la pantalla. Si el usuario no esté tocando la pantalla, el `TouchCollection` estará vacío, en cuyo caso no deberíamos pasamos el carácter.

Si el usuario toca la pantalla, se moverá el carácter hacia la primera entrada táctil, en otras palabras, el `TouchLocation` en el índice 0. Inicialmente, estableceremos la velocidad deseada para que sea igual a la diferencia entre la ubicación del carácter y la ubicación del primer toque:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Lo que sigue es un poco de matemáticas, lo que mantendrán el carácter que se mueve a la misma velocidad. Para ayudar a explicar por qué esto es importante, consideremos una situación donde el usuario toca el 500 píxeles de pantalla lejos de donde se encuentra el carácter. La primera línea where `desiredVelocity.X` es conjunto asignaría un valor de 500. Sin embargo, si el usuario toca la pantalla a una distancia de sólo 100 unidades de los caracteres, el `desiredVelocity.X `se establecería en 100. El resultado sería que velocidad de movimiento del carácter respondería a cómo lejos que es el punto de toque de caracteres. Puesto que deseamos que el carácter que se va a mover siempre la misma velocidad, es necesario modificar el desiredVelocity.

El `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instrucción está comprobando si la velocidad es que no sea cero, en otras palabras, se comprueba para asegurarse de que el usuario no toca la misma posición como la posición del carácter actual. Si no, a continuación, necesitamos establecer la velocidad del carácter sea constante independientemente de cómo de lejos la entrada táctil es. Esto se logra la normalización del vector de velocidad, que da como resultado lo que se va a una longitud de 1. Un vector de velocidad de 1 significa que el carácter se trasladarán a 1 píxel por segundo. Se podrá acelerar la multiplicando el valor por la velocidad deseada de 200.


### <a name="applying-velocity-to-position"></a>Aplicar Velocity a posición

La velocidad devuelto desde `GetDesiredVelocityFromInput` debe aplicarse al carácter `X` y `Y` valores para que surta efecto en tiempo de ejecución. Modificaremos el `Update` método como sigue:

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

¿Qué hemos hecho aquí se denomina *basado en tiempo* movimiento (en contraposición a *basado en fotogramas* movimiento). Movimiento de duración definida multiplica un valor de velocidad (en nuestro caso de los valores almacenados en el `velocity` variable) por cuánto tiempo ha transcurrido desde la última actualización que se almacena en `gameTime.ElapsedGameTime.TotalSeconds`. Si el juego se ejecuta en menos fotogramas por segundo, aumenta el tiempo transcurrido entre marcos: el resultado final es que los objetos mediante movimiento basado en tiempo siempre se trasladarán a la misma velocidad, independientemente de la velocidad de fotogramas.

Si ahora ejecutamos nuestro juego, veremos que el carácter está avanzando hacia la ubicación de toque:

![](part2-images/image6.gif "El carácter se mueve hacia la ubicación de toque")

## <a name="matching-movement-and-animation"></a>Coincidencia de movimiento y la animación

Una vez que tenemos nuestro carácter moviendo y reproducir una animación única, podemos definir el resto de nuestro animaciones y luego usarlos para reflejar el movimiento del carácter. Cuando termine de tendremos ocho animaciones en total:

- Animaciones para recorrer hacia arriba, abajo, izquierda y derecha
- Las animaciones elegibles todavía y hacia arriba, abajo, izquierda y derecha

### <a name="defining-the-rest-of-the-animations"></a>Definir el resto de las animaciones

En primer lugar, vamos a agregar el `Animation` instancias para el `CharacterEntity` clase para todos nuestras animaciones en el mismo lugar donde se ha agregado `walkDown`. Una vez hecho esto, el `CharacterEntity` tendrá el siguiente `Animation` miembros:

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Ahora definiremos las animaciones en el `CharacterEntity` constructor como sigue:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Debemos mencionar que el código anterior se ha agregado a la `CharacterEntity` constructor para que este tutorial más corto. Normalmente los juegos se separar la definición de animaciones de personajes en sus propias clases o cargar esta información desde un formato de datos como XML o JSON.

A continuación, se podrá ajustar la lógica para usar las animaciones de acuerdo con la dirección que se mueve el carácter o de acuerdo con la última animación si acaba de finalizar el carácter. Para ello, modificaremos el `Update` método:


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

El código para cambiar las animaciones se divide en dos bloques. El primero comprueba si la velocidad no es igual a `Vector2.Zero` : Esto nos indica si el carácter es móvil. Si el carácter es móvil, a continuación, podemos mirar la `velocity.X` y `velocity.Y` valores para determinar qué pasear animación se reproduzca.

Si no se mueve el carácter y, después, queremos establecer el carácter `currentAnimation` a una animación permanente: pero haremos lo solo si la `currentAnimation` es un recorrido de la animación o si no se ha establecido una animación. Si el `currentAnimation` no es una de las cuatro pasear animaciones, a continuación, el carácter ya es permanente, es necesario cambiar `currentAnimation`.

El resultado de este código es que el carácter se animar correctamente al recorrer y, a continuación, se enfrentan a la última dirección que se ha recorrido cuando se detiene:

![](part2-images/image7.gif "El resultado de este código es que el carácter se animar correctamente al recorrer y, a continuación, se enfrentan a la última dirección que se ha recorrido cuando se detiene")

## <a name="summary"></a>Resumen

En este tutorial se ha mostrado cómo trabajar con MonoGame para crear una multiplataforma juegos con sprites, movimiento de objetos, la detección de entrada y la animación. Describe la creación de una clase de animación de uso general. También se ha explicado cómo crear una entidad de caracteres para organizar la lógica del código.

## <a name="related-links"></a>Vínculos relacionados

- [Recurso de imagen CharacterSheet (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Recorrido completo juego (ejemplo)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
