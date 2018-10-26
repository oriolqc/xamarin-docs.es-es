---
title: Entidades de CocosSharp
description: El modelo de entidad es una manera eficaz para organizar el código de juego. Mejora la legibilidad, hace que el código sea más fácil de mantener y aprovecha la funcionalidad integrada de elementos primarios/secundarios.
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 6445d595c9d8ca47e187fdcd158cd5a801a96407
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103210"
---
# <a name="entities-in-cocossharp"></a>Entidades de CocosSharp

_El modelo de entidad es una manera eficaz para organizar el código de juego. Mejora la legibilidad, hace que el código sea más fácil de mantener y aprovecha la funcionalidad integrada de elementos primarios/secundarios._

El modelo de entidad puede mejorar los esfuerzos del desarrollador con CocosSharp a través de la organización de código mejorada. En este tutorial será un ejemplo práctico que muestra cómo crear dos entidades: una entidad de envío y una entidad de viñeta. Estas entidades serán objetos independientes, lo que significa que se crea una instancia de una vez que se representa automáticamente y llevará a cabo la lógica de movimiento según corresponda para su tipo. 

Esta guía trata los temas siguientes:

 - Introducción a las entidades de juegos
 - General frente a tipos de entidad específico
 - Configuración del proyecto
 - Crear clases de entidad
 - Agregar instancias de entidad a la `GameLayer`
 - Reacción a la lógica de la entidad en el `GameLayer`

El juego terminado tendrá este aspecto:

![](entities-images/image1.png "El juego terminado tendrá un aspecto similar al siguiente")


## <a name="introduction-to-game-entities"></a>Introducción a las entidades de juegos

Juegos las entidades son clases que definen los objetos que necesitan la lógica de representación, colisiones, físicas o inteligencia artificial. Afortunadamente, las entidades presentes en la base de código de un juego a menudo coinciden con los objetos conceptuales en un juego. Cuando es true, identificar las entidades necesarias en un juego es más fácil posible. 

Por ejemplo, un espacio con temas [shoot clips juego](http://en.wikipedia.org/wiki/Shoot_%27em_up) puede incluir las siguientes entidades:

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

Estas entidades sería sus propias clases en el juego, y cada instancia requeriría poca o ninguna configuración más allá de la creación de instancias.


## <a name="general-vs-specific-entity-types"></a>General frente a tipos de entidad específico

Una de las primeras preguntas que se enfrentan los desarrolladores de juegos con un sistema de la entidad es la cantidad para generalizar sus entidades. La más específica de implementaciones definiría clases para cada tipo de entidad, incluso si se diferencian en algunas características. Los sistemas más generales se combinar grupos de entidades en una clase y permitir que las instancias para personalizarse.

Por ejemplo, que podemos imaginar un juego de espacio que define las clases siguientes:

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

Un enfoque más generalizado sería crear una clase única para el Reproductor buques y una sola clase buques enemigos, que puede configurarse para admitir tipos de envío diferentes. Personalización puede incluir la imagen para cargar, qué tipo de entidades de viñeta que se va a crear cuando se capta coeficientes de movimiento, y se incluye la lógica de inteligencia artificial para el enemigo. En este caso se puede reducir la lista de entidades para:

 - `PlayerShip`
 - `EnemyShip`

Por supuesto, estos tipos de entidad se pueden generalizar aún más, ya que permite la personalización de por instancia para controlar el movimiento. Las instancias de envío del Reproductor leería de entrada mientras envío enemigos instancias pueden realizar la lógica de inteligencia artificial. Esto significa que las entidades podrían ser generalizadas aún más en una sola clase:

 - `Ship`

La generalización puede continuar aún más: un juego puede usar una única clase base para todas las entidades. Esta clase única, que se puede llamar a `GameEntity`, sería la clase utilizada para cada instancia de entidad en el juego completo, incluidas las entidades que no se incluye como viñetas y recopilables elementos (potenciadores).

Esto general de la mayoría de los sistemas es a menudo conoce como un *sistema componente*. En este sistema, juegos entidades pueden tener los distintos componentes como física, AI, o representar los componentes agregados para personalizar la apariencia y comportamiento. Sistemas basados en componentes puros permiten una flexibilidad óptima y pueden evitar problemas causados por el uso de la herencia, como las cadenas de herencia compleja. Al igual que con otros generalizaciones, sistemas de componente eficaz pueden ser difíciles de configurar y pueden reducir la expresividad de código.

El nivel de generalización utilizado depende de muchas consideraciones, incluidos:

 - Tamaño del juego: juegos más pequeños pueden permitirse crear clases específicas, mientras que los juegos más grandes pueden ser difíciles de administrar con un gran número de clases.
 - Data driven development: juegos que se basan en datos (imágenes, modelos 3D y los archivos de datos como JSON o XML) pueden beneficiarse de tener generalizado tipos de entidad y configurar los aspectos específicos según los datos. Esto es especialmente importar para juegos que se va a agregar el nuevo contenido durante el desarrollo o después de que se ha liberado el juego.
 - Patrones de motor de juego: algunos motores de juegos muy recomendable el uso de los sistemas de componente mientras que otras permiten a los desarrolladores decidir cómo organizar las entidades. CocosSharp no requiere el uso de un sistema de componente, por lo que los desarrolladores pueden implementar cualquier tipo de entidad. 

Por simplicidad, vamos a usar un enfoque basado en clases específico con una sola entidad de envío y viñetas para este tutorial.


## <a name="project-setup"></a>Configuración del proyecto

Antes de comenzar la implementación de nuestras entidades, es necesario crear un proyecto. Vamos a usar las plantillas de proyecto CocosSharp para simplificar la creación del proyecto. [Consulte esta publicación](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio) para obtener información sobre cómo crear un proyecto CocosSharp desde Visual Studio para las plantillas de Mac. El resto de esta guía utilizará el nombre del proyecto **EntityProject**.

Una vez creado el proyecto también se establece la resolución de nuestro juego para ejecutarse a 320 x 480. Para ello, llame a `CCScene.SetDefaultDesignResolution` en el `GameAppDelegate.ApplicationDidFinishLaunching` método como sigue:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

Para obtener más información sobre cómo tratar con CocosSharp resoluciones, vea nuestra [guía sobre el control de varias resoluciones en CocosSharp](~/graphics-games/cocossharp/resolutions.md).


## <a name="adding-content-to-the-project"></a>Agregar contenido al proyecto

Una vez creado el proyecto, se agregará a los archivos contenidos en [este archivo zip contenido](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true). Para ello, descargue el archivo zip y descomprímalo. Agregar ambos **ship.png** y **bullet.png** a la **contenido** carpeta. El **contenido** carpeta estará dentro de la **activos** carpeta en Android y estará en la raíz del proyecto de iOS. Una vez agregado, deberíamos ver ambos archivos en el **contenido** carpeta:

![](entities-images/image2.png "Una vez agregado, ambos archivos deben estar en la carpeta de contenido")


## <a name="creating-the-ship-entity"></a>Creación de la entidad de envío

La `Ship` clase será la primera entidad de nuestro del juego. Para agregar un `Ship` class, primero cree una carpeta denominada **entidades** en el nivel raíz del proyecto. Agregue una nueva clase en el **entidades** carpeta denominada `Ship`:

![](entities-images/image3.png "Agregue una nueva clase en la carpeta de entidades denominada directa")

El primer cambio que haremos en nuestro `Ship` clase consiste en dejar que se heredan de la `CCNode` clase. `CCNode` al igual que actúa como clase base para clases comunes de CocosSharp `CCSprite` y `CCLayer`y nos proporciona la funcionalidad siguiente:

 - `Position` propiedad para mover la nave alrededor de la pantalla.
 - `Children` propiedad para agregar un `CCSprite.`
 - `Parent` propiedad, que puede utilizarse para adjuntar `Ship` instancias Sí `CCNodes`. No se usará con esta característica en este tutorial; juegos mayor a menudo aprovechar las ventajas de adjuntar entidades Sí `CCNodes`. 
 - `AddEventListener` método para responder a la entrada para mover el envío.
 - `Schedule` método para la grabación de viñetas.

También agregaremos un `CCSprite` para que nuestro directa se puede ver en pantalla de instancia:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

A continuación, agregaremos el envío a nuestro `GameLayer` para ver cómo se mostrarán en nuestro juego:


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

Si ejecutamos nuestro juego Ahora veremos nuestra entidad de envío:

![](entities-images/image4.png "Cuando se ejecuta el juego, se mostrará la entidad de envío")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>¿Por qué se heredan de CCNode en lugar de CCSprite?

En este momento nuestros `Ship` clase es simplemente un contenedor para un `CCSprite` instancia. Puesto que `CCSprite` también hereda de `CCNode`, nos podríamos haber heredado directamente de `CCSprite`, lo que habría reduce el código en `Ship.cs`. Además, heredar directamente de `CCSprite` reduce el número de objetos en memoria y mejorar el rendimiento, reducir el tamaño del árbol de dependencias.

A pesar de estas ventajas, se hereda de `CCNode` para ocultar algunas de las `CCSprite` las propiedades de cada instancia. Por ejemplo, el `Texture` no se debe modificar la propiedad fuera de la `Ship` clase y heredar de `CCNode` nos permite ocultar esta propiedad. Se convierten en los miembros públicos de nuestras entidades especialmente importantes a medida que crece un juego y desarrolladores adicionales se agregan a un equipo.


## <a name="adding-input-to-the-ship"></a>Adición de entrada para el envío

Ahora que nuestro ship está visible en pantalla iremos agregando entrada. Nuestro enfoque será similar al enfoque adoptado en el [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md), excepto en que se va a colocar el código para el movimiento en el `Ship` clase en lugar de en el que contiene `CCLayer` o `CCScene`.

Agregue el código a `Ship` para admitir moverlo a siempre que el usuario está tocando la pantalla:


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

Muchos clips aumentarán drásticamente juegos implementan una velocidad máxima, mediante la imitación de movimiento basado en controlador tradicional. Es decir, simplemente implementaremos el movimiento de inmediato para mantener el código más corto.


## <a name="creating-the-bullet-entity"></a>Creación de la entidad de viñeta

La segunda entidad en nuestro juego sencillo es una entidad para mostrar las viñetas. Al igual que el `Ship` entidad, el `Bullet` entidad contendrá un `CCSprite` para que aparezca en pantalla. La lógica para el movimiento de diferencia en que no depende de entrada del usuario para el movimiento. en su lugar, `Bullet` moverán instancias en una línea recta con propiedades de velocidad.

Primero vamos a agregar un nuevo archivo de clase para nuestro **entidades** carpeta y llámelo **viñeta**:

![](entities-images/image5.png "Agregue un nuevo archivo de clase a la carpeta de entidades y llámelo viñeta")

Una vez agregado, modificaremos el `Bullet.cs` como sigue:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

Además de cambiar el archivo usado para la `CCSprite` a `bullet.png`, el código en `ApplyVelocity` incluye la lógica de movimiento que se basa en dos coeficientes: `VelocityX` y `VelocityY`.

El `Schedule` método permite agregar delegados a llamarse cada fotograma. En este caso vamos a agregar el `ApplyVelocity` método para que nuestro viñeta mueve según sus valores de velocidad. El `Schedule` método toma un `Action<float>`, donde el parámetro float especifica la cantidad de tiempo (en segundos) transcurrido desde el último fotograma, que se utiliza para implementar el movimiento basado en tiempo. Desde el momento en el valor se mide en segundos, a continuación, los valores de velocidad representan el movimiento en *píxeles por segundo*.


## <a name="adding-bullets-to-gamelayer"></a>Agregar viñetas a GameLayer

Antes de agregar cualquier `Bullet` instancias a nuestro juego realizaremos un contenedor, específicamente un `List<Bullet>`. Modificar el `GameLayer` por lo que incluye una lista de viñetas:


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

A continuación, deberá rellenar la `Bullet` lista. La lógica para cuándo se debe crear un `Bullet` debe estar incluido en el `Ship` entidad, pero la `GameLayer` es responsable de almacenar la lista de viñetas. Usaremos el patrón de fábrica para permitir la `Ship` entidad que se va a crear `Bullet` instancias. Este generador va a exponer un evento que el `GameLayer` puede controlar. 

Para hacerlo en primer lugar, vamos a agregar una carpeta a nuestro proyecto denominado **generadores**, y, a continuación, agregue una nueva clase denominada `BulletFactory`:

![](entities-images/image6.png "Agregar una carpeta al proyecto denominado generadores y, a continuación, agregue una nueva clase denominada BulletFactory")

A continuación, implementaremos la `BulletFactory` clase singleton:


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

El `Ship` entidad controlará crear `Bullet` instancias, en concreto, va a controlar con qué frecuencia `Bullet` (es decir, ¿con qué frecuencia se desencadena la viñeta), se deben crear instancias, su posición y su progreso.

Modificar el `Ship` constructor de la entidad para agregar un nuevo `Schedule` llamar y, a continuación, implemente este método como sigue:


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

El último paso consiste en controlar la creación de nuevos `Bullet` instancias en el `GameLayer` código. Agregar un controlador de eventos para el `BulletCreated` eventos que agrega el recién creado `Bullet` a las listas correspondientes:


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

Ahora podemos ejecutar el juego y vea el `Ship` filmar `Bullet` instancias:

![](entities-images/image1.png "Ejecutar el juego y el envío se solución de instancias de viñeta")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>¿Por qué GameLayer tiene miembros directa y viñetas

Nuestro `GameLayer` clase define dos campos para contener referencias a las instancias de entidad (`ship` y `bullets`), pero no hace nada con ellos. Además, las entidades son responsables de su propio comportamiento como movimiento y la solución. Así que ¿por qué se agregó `ship` y `bullets` campos a `GameLayer`?

El motivo se agregan a estos miembros es que una implementación del juego completa requeriría lógica en el `GameLayer` para la interacción entre las distintas entidades. Por ejemplo, este juego se puede desarrollar aún más para incluir los enemigos que se pueden destruir el Reproductor. Estos enemigos estaría incluidos en un `List` en el `GameLayer`y la lógica para probar si `Bullet` entren en conflicto con las instancias de los enemigos se realizarían en el `GameLayer` también. En otras palabras, el `GameLayer` es la raíz *propietario* de entidad de todas las instancias y es responsable de las interacciones entre instancias de entidad.


## <a name="bullet-destruction-considerations"></a>Consideraciones de destrucción de viñeta

Nuestro juego actualmente no tiene código para destruir `Bullet` instancias. Cada `Bullet` instancia tiene lógica para mover en la pantalla, pero que no hemos agregado código alguno para destruir cualquiera fuera de la pantalla `Bullet` instancias.

Además, la destrucción de `Bullet` instancias no pueden pertenecer en `GameLayer`. Por ejemplo, en lugar de que se destruyen cuando fuera de la pantalla, el `Bullet` entidad puede tener lógica para que se destruya después un determinado período de tiempo. En este caso, el `Bullet` necesita una manera de comunicar que debe destruirse a la `GameLayer`, parecida a la `Ship` entidad comunica al `GameLayer` que un nuevo `Bullet` se creó a través de la `BulletFactory`.

La solución más sencilla es expandir la responsabilidad de la clase de generador para admitir la destrucción. A continuación, el generador puede recibir una notificación de una instancia de entidad que se destruyan, que puede controlarse mediante otros objetos, como el `GameLayer` quitar la instancia de entidad de las listas. 

## <a name="summary"></a>Resumen

Esta guía muestra cómo crear entidades de CocosSharp heredando de la `CCNode` clase. Estas entidades son objetos independientes, controlar la creación de sus propios objetos visuales y la lógica personalizada. Esta guía designa código perteneciente dentro de una entidad (movimiento y la creación de otras entidades) desde el código que pertenece en el contenedor de entidades raíz (colisiones y otra lógica de interacción de entidad).

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Contenido zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
