---
title: Entidades de CocosSharp
description: El modelo de entidad es una manera eficaz para organizar el código de juego. Mejora la legibilidad, hace que el código sea más fácil de mantener y aprovecha la funcionalidad integrada de elemento primario/secundario.
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 406c416e9bbecb4726793981ae347e0172ab0dbc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="entities-in-cocossharp"></a>Entidades de CocosSharp

_El modelo de entidad es una manera eficaz para organizar el código de juego. Mejora la legibilidad, hace que el código sea más fácil de mantener y aprovecha la funcionalidad integrada de elemento primario/secundario._

El modelo de entidad puede mejorar los esfuerzos de un desarrollador con CocosSharp a través de la organización de código mejorada. En este tutorial será un ejemplo práctico, que muestra cómo crear dos entidades: una entidad de envío y una entidad de viñeta. Estas entidades será objetos independientes, lo que significa que una vez crea una instancia se representará automáticamente y realizará la lógica de movimiento según corresponda para su tipo. 

Esta guía trata los temas siguientes:

 - Introducción a las entidades de juegos
 - Seguridad general frente a tipos de entidad específico
 - Programa de instalación de proyecto
 - Crear clases de entidad
 - Agregar instancias de entidad para el `GameLayer`
 - Reaccionar a la lógica de la entidad en el `GameLayer`

El juego terminado tendrá un aspecto similar al siguiente:

![](entities-images/image1.png "El juego terminado tendrá un aspecto similar al siguiente")


## <a name="introduction-to-game-entities"></a>Introducción a las entidades de juegos

Entidades de juegos son clases que definen los objetos que necesitan la lógica de representación, colisión, física o inteligencia artificial. Afortunadamente, las entidades presentes en la base de código de un juego a menudo coinciden con los objetos conceptuales en un juego. Cuando es true, las entidades necesarias en un juego puede más fácil identificar. 

Por ejemplo, un espacio con temas [enceste clips juego](http://en.wikipedia.org/wiki/Shoot_%27em_up) puede incluir las siguientes entidades:

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

Estas entidades sería sus propias clases en el juego, y cada instancia requeriría poca o ninguna configuración más allá de la creación de instancias.


## <a name="general-vs-specific-entity-types"></a>Seguridad general frente a tipos de entidad específico

Una de las primeras preguntas que se enfrentan los desarrolladores de juegos con un sistema de entidad es la cantidad para generalizar sus entidades. El más específico de implementaciones tendría que definir clases para todos los tipos de entidad, aunque difieren en algunas características. Sistemas más generales se combine los grupos de entidades en una clase y permite instancias personalizarse.

Por ejemplo, que podemos imaginar un juego de espacio que define las clases siguientes:

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

Un enfoque más generalizado sería crear una sola clase para buques de Reproductor y una sola clase buques adversario, que puede configurarse para admitir tipos de envío diferentes. Personalización puede incluir la imagen para cargar, qué tipo de entidades de viñeta que se va a crear cuando se capta coeficientes de movimiento, y lógica de AI para el enemigo suministra. En este caso se puede reducir la lista de entidades para:

 - `PlayerShip`
 - `EnemyShip`

Por supuesto, estos tipos de entidad pueden ser aún más generalizados que permiten la personalización de por instancia para controlar el movimiento. Instancias de envío del Reproductor leería de entrada mientras instancias de envío adversario pueden realizar la lógica de AI. Esto significa que las entidades podrían estar generalizadas aún más en una sola clase:

 - `Ship`

La generalización puede continuar aún más: un juego puede usar una sola clase base para todas las entidades. Esta clase única, que se puede llamar `GameEntity`, sería la clase utilizada para cada instancia de entidad en toda la partida, incluidas las entidades que no sean se distribuye como viñetas y elementos recopilables (power-SAI o ups).

Esto general la mayoría de sistemas es a menudo denomina un *sistema componente*. En este sistema, juegos entidades pueden tener componentes individuales como física, AI, o representar los componentes agregados para personalizar la apariencia y comportamiento. Puros sistemas equipados con el componente de habilitar una flexibilidad óptima y pueden evitar problemas causados por el uso de la herencia como cadenas de herencia compleja. Al igual que con otros generalizaciones, sistemas de componente efectivo pueden ser difíciles de configurar y pueden reducir la expresividad de código.

El nivel de generalización utilizado depende de muchas consideraciones, incluidos:

 - Tamaño: portal de juego pueden permitirse juegos más pequeños crear clases específicas, mientras que los juegos más grandes pueden ser difíciles de administrar con un gran número de clases.
 - Datos de desarrollo: controlado por juegos que se basan en datos (imágenes, modelos 3D y archivos de datos como JSON o XML) pueden beneficiarse de tener generalizado tipos de entidad y la configuración de las características basadas en datos. Esto es especialmente importante para juegos que se va a agregar contenido nuevo durante el desarrollo o después de que se ha liberado el juego.
 - Patrones de motor de juegos: algunos motores de juegos recomendamos encarecidamente el uso de los sistemas de componente mientras que otras permiten a los desarrolladores decidir cómo organizar las entidades. CocosSharp no requieren el uso de un sistema de componente, por lo que los desarrolladores pueden implementar cualquier tipo de entidad. 

Por simplicidad, utilizaremos un enfoque basado en clases específico con una sola entidad de envío y viñetas para este tutorial.


## <a name="project-setup"></a>Programa de instalación de proyecto

Antes de empezar a implementar nuestras entidades, es necesario crear un proyecto. Usaremos las plantillas de proyecto CocosSharp para simplificar la creación del proyecto. [Compruebe esta entrada](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio) para obtener información sobre cómo crear un proyecto de CocosSharp de Visual Studio para las plantillas de Mac. El resto de esta guía va a usar el nombre del proyecto **EntityProject**.

Una vez creado el proyecto se establecerá la resolución de nuestro juego para ejecutarse en 320 x 480. Para ello, llame a `CCScene.SetDefaultDesignResolution` en el `GameAppDelegate.ApplicationDidFinishLaunching` método tal como se indica a continuación:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

Para obtener más información sobre cómo tratar con las soluciones de CocosSharp, consulte nuestro [guía en el control de varias resoluciones en CocosSharp](~/graphics-games/cocossharp/resolutions.md).


## <a name="adding-content-to-the-project"></a>Agregar contenido al proyecto

Una vez creado el proyecto, se agregará a los archivos contenidos en [este archivo zip contenido](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true). Para ello, descargue el archivo zip y descomprímalo. Agregarlos **ship.png** y **bullet.png** a la **contenido** carpeta. El **contenido** carpeta estará dentro de la **activos** carpeta en Android y estarán en la raíz del proyecto de iOS. Una vez agregados, deberíamos ver ambos archivos en el **contenido** carpeta:

![](entities-images/image2.png "Una vez agregados, ambos archivos deben estar en la carpeta de contenido")


## <a name="creating-the-ship-entity"></a>Creación de la entidad de envío

La `Ship` clase será la primera entidad de nuestro del juego. Para agregar una `Ship` de clases, primero cree una carpeta denominada **entidades** en el nivel raíz del proyecto. Agregue una nueva clase en el **entidades** carpeta denominada `Ship`:

![](entities-images/image3.png "Agregue una nueva clase en la carpeta de entidades llamada directa")

El primer cambio que haremos en nuestro `Ship` clase consiste en dejar que heredan de la `CCNode` clase. `CCNode` actúa como clase base para clases de CocosSharp comunes, como `CCSprite` y `CCLayer`y nos proporciona la funcionalidad siguiente:

 - `Position` propiedad para mover el envío por la pantalla.
 - `Children` propiedad para agregar un `CCSprite.`
 - `Parent` propiedad, que puede utilizarse para adjuntar `Ship` instancias Sí `CCNodes`. No puede usar esta característica en este tutorial; juegos de mayor a menudo aprovechar las ventajas de adjuntar entidades Sí `CCNodes`. 
 - `AddEventListener` método para responder a la entrada para mover el envío.
 - `Schedule` método para iniciar la grabación viñetas.

Agregaremos también un `CCSprite` por lo que nuestro ship puede verse en pantalla de instancia:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

A continuación, agregaremos el envío a nuestro `GameLayer` para que se muestre en el juego:


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

Si ejecutamos nuestro juego que se verán en la entidad de envío:

![](entities-images/image4.png "Al ejecutar el juego, se mostrará la entidad de envío")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>¿Por qué se heredan de CCNode en lugar de CCSprite?

En este momento nuestro `Ship` clase es un contenedor simple para una `CCSprite` instancia. Puesto que `CCSprite` también hereda de `CCNode`, nos podríamos haber heredado directamente de `CCSprite`, que habría reduce el código en `Ship.cs`. Además, heredar directamente de `CCSprite` reduce el número de objetos en memoria y puede mejorar el rendimiento al hacer que el árbol de dependencias más pequeños.

A pesar de estos beneficios, se hereda de `CCNode` para ocultar algunas de las `CCSprite` propiedades de cada instancia. Por ejemplo, el `Texture` no se debe modificar la propiedad fuera de la `Ship` clase y heredar de `CCNode` nos permite ocultar esta propiedad. Los miembros públicos de nuestro entidades son especialmente importantes crece un juego a medida y los desarrolladores adicionales se agregan a un equipo.


## <a name="adding-input-to-the-ship"></a>Agregando la entrada para el envío

Ahora que está visible en la pantalla nuestro ship iremos agregando entrada. Nuestro enfoque será similar al enfoque adoptado en la [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md), excepto en que se va a colocar el código para que entren en el `Ship` clase en lugar de en la que contiene `CCLayer` o `CCScene`.

Agregue el código a `Ship` para admitir moverlo a, donde el usuario toca la pantalla:


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

Muchos disparar clips de juegos implemente una velocidad máxima, imitando movimiento tradicional basado en el controlador. Es decir, simplemente implementaremos movimiento inmediato para mantener el código más corto.


## <a name="creating-the-bullet-entity"></a>Creación de la entidad de viñeta

La segunda entidad en nuestro juego simple es una entidad para mostrar viñetas. Al igual que el `Ship` entidad, el `Bullet` entidad contendrá un `CCSprite` para que aparezca en pantalla. La lógica para que entren difiere en que no depende de proporcionados por el usuario para el movimiento; en su lugar, `Bullet` instancias se moverán en una línea recta con propiedades de progreso.

Primero vamos a agregar un nuevo archivo de clase para nuestro **entidades** carpeta y llámelo **viñeta**:

![](entities-images/image5.png "Agregue un nuevo archivo de clase a la carpeta de entidades y llámelo viñeta")

Una vez agregado, modificaremos la `Bullet.cs` código como se indica a continuación:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

Además de cambiar el archivo utilizado para la `CCSprite` a `bullet.png`, el código en `ApplyVelocity` incluye la lógica de movimiento que se basa en dos coeficientes: `VelocityX` y `VelocityY`.

El `Schedule` método permite agregar delegados a llamarse cada fotograma. En este caso estamos agregando el `ApplyVelocity` método para que nuestro viñeta mueve según sus valores de progreso. El `Schedule` método toma un `Action<float>`, donde el parámetro float especifica la cantidad de tiempo (en segundos) transcurrido desde el último fotograma, que se utiliza para implementar el movimiento, basándose en el tiempo. Desde el momento en el valor se mide en segundos, a continuación, los valores de velocidad representan el movimiento en *píxeles por segundo*.


## <a name="adding-bullets-to-gamelayer"></a>Agregar viñetas a GameLayer

Antes de que se agregue cualquier `Bullet` instancias de nuestro juego se realizará un contenedor, específicamente una `List<Bullet>`. Modificar el `GameLayer` por lo que se incluye una lista de viñetas:


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

A continuación, se necesitará rellenar el `Bullet` lista. La lógica para cuándo se debe crear un `Bullet` debe incluirse en la `Ship` entidad, pero la `GameLayer` es responsable de almacenar la lista de viñetas. Usaremos el patrón de fábrica para permitir la `Ship` entidad que se va a crear `Bullet` instancias. Este generador expondrá un evento que la `GameLayer` puede controlar. 

Para hacer esto en primer lugar, vamos a agregar una carpeta a nuestro proyecto denominado **generadores**, y, a continuación, agregue una nueva clase denominada `BulletFactory`:

![](entities-images/image6.png "Agregar una carpeta al proyecto denominado generadores y, a continuación, agregue una nueva clase denominada BulletFactory")

A continuación, implementaremos el `BulletFactory` clase singleton:


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

El `Ship` entidad controlará crear `Bullet` instancias: en concreto, controlará la frecuencia con `Bullet` instancias deben crearse (es decir, ¿con qué frecuencia se desencadena la viñeta), su posición y su progreso.

Modificar el `Ship` constructor de la entidad para agregar un nuevo `Schedule` llamar y, a continuación, implementar este método como sigue:


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

El último paso consiste en controlar la creación de nuevos `Bullet` instancias en el `GameLayer` código. Agregar un controlador de eventos para el `BulletCreated` eventos que agrega recién creado `Bullet` a las listas correspondientes:


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

Ahora podemos ejecutar el juego y vea el `Ship` solución `Bullet` instancias:

![](entities-images/image1.png "Ejecutar el juego y el envío se puede grabar instancias de viñeta")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>¿Por qué GameLayer tiene miembros se incluyen y viñetas

Nuestro `GameLayer` clase define dos campos para contener referencias a nuestro instancias de entidad (`ship` y `bullets`), pero no hace nada con ellos. Además, las entidades son responsables de su propio comportamiento como movimiento y la solución. Por lo tanto ¿por qué se agregó `ship` y `bullets` campos a `GameLayer`?

La razón agregamos estos miembros es que una implementación del juego completa requeriría lógica en la `GameLayer` para la interacción entre las distintas entidades. Por ejemplo, este juego se puede desarrollar aún más para incluir enemigos que pueden ser destruidos por el Reproductor. Estos enemigos estaría incluidos en un `List` en el `GameLayer`y la lógica para probar si `Bullet` instancias entran en conflicto con los enemigos se realizará en el `GameLayer` así. En otras palabras, el `GameLayer` es la raíz *propietario* de entidad de todas las instancias y es responsable de las interacciones entre instancias de la entidad.


## <a name="bullet-destruction-considerations"></a>Consideraciones de destrucción de viñeta

Nuestro juego actualmente no tiene código para destruir `Bullet` instancias. Cada `Bullet` instancia tiene una lógica para mover en pantalla, pero no hemos agregamos cualquier código para destruir cualquiera fuera de la pantalla `Bullet` instancias.

Además, la destrucción de `Bullet` instancias no pueden pertenecer en `GameLayer`. Por ejemplo, en lugar de que se destruya cuando fuera de la pantalla, la `Bullet` entidad puede tener lógica para destruir propio después de una cierta cantidad de tiempo. En este caso, el `Bullet` necesita una manera de comunicar que debe destruirse a la `GameLayer`, similar a la `Ship` entidad se comunica a la `GameLayer` que un nuevo `Bullet` creada mediante el `BulletFactory`.

La solución más sencilla consiste en expandir la responsabilidad de la clase de generador para admitir la destrucción. A continuación, el generador puede recibir una notificación de una instancia de entidad que se está destruyendo, que pueda ser controlada por otros objetos, como el `GameLayer` quitar la instancia de entidad de las listas. 

## <a name="summary"></a>Resumen

Esta guía muestra cómo crear entidades CocosSharp heredando de la `CCNode` clase. Estas entidades son objetos independientes, control de creación de sus propios objetos visuales y la lógica personalizada. Esta guía designa código que pertenece dentro de una entidad (movimiento y la creación de otras entidades) desde el código que pertenece el contenedor de entidades raíz (colisión y cualquier otra lógica de interacción de entidad).

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Zip contenido](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
