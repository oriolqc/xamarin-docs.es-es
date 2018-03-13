---
title: "Detalles de implementación de tiempo de moneda"
description: "Esta guía describe los detalles de implementación en el juego de tiempo de moneda, incluidos trabajar con mapas de mosaico, creación de entidades, animar sprites y colisión eficaz de implementar."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: b3827d05ae9e563ae04dd4ab1e303577f6c9d82a
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="coin-time-implementation-details"></a>Detalles de implementación de tiempo de moneda

_Esta guía describe los detalles de implementación en el juego de tiempo de moneda, incluidos trabajar con mapas de mosaico, creación de entidades, animar sprites y colisión eficaz de implementar._

Hora de la moneda es un juego para iOS y Android reformador de platino completa. El objetivo del juego consiste en recopilar todas las monedas en un nivel y, a continuación, llegar a la puerta de salida evitando enemigos y obstáculos al mismo.

![](cointime-images/image1.png "El objetivo del juego consiste en recopilar todas las monedas en un nivel y, a continuación, llegar a la puerta de salida al evitar enemigos y obstáculos")

Esta guía describe los detalles de la implementación en tiempo de moneda, que abarcan los temas siguientes:

- [Trabajar con archivos TMX](#Working_with_TMX_Files)
- [Nivel de carga](#Level_Loading)
- [Agregar nuevas entidades](#Adding_New_Entities)
- [Entidades animadas](#Animated_Entities)


# <a name="content-in-coin-time"></a>Contenido en tiempo de moneda

Tiempo de moneda es un proyecto de ejemplo que representa cómo puede estar organizado de un proyecto de CocosSharp completo. La hora de moneda pretende simplificar la adición y el mantenimiento del contenido de la estructura. Usa **.tmx** archivos creados por [mosaico](http://www.mapeditor.org) para niveles y los archivos XML para definir las animaciones. Modificar o agregar nuevo contenido se puede lograr con el mínimo esfuerzo. 

Si bien este enfoque hace tiempo moneda un proyecto de efectivo para aprendizaje y experimentación, también refleja cómo profesionales juegos se realizan. Esta guía explica algunos de los métodos que se emplean para simplificar la adición y modificar el contenido.


# <a name="working-with-tmx-files"></a>Trabajar con archivos TMX

Niveles de tiempo de moneda se definen mediante el formato de archivo .tmx, que se genera mediante el [mosaico](http://www.mapeditor.org) editor de mapa de mosaico. Para obtener una explicación detallada sobre cómo trabajar con mosaico, consulte el [utilizando coloca en mosaico con guía Cocos agudo](~/graphics-games/cocossharp/tiled.md). 

Cada nivel se define en su propio archivo .tmx contenido en el **CoinTime/activos/contenido/niveles** carpeta. Todos los niveles de tiempo de moneda compartan un archivo de tileset, que se define en el **mastersheet.tsx** archivo. Este archivo define las propiedades personalizadas de cada icono, por ejemplo, si el icono tiene colisión sólido o si el icono debe ser reemplazado por una instancia de entidad. El archivo de mastersheet.tsx permite propiedades puede definirse una sola vez y se utilizan en todos los niveles. 


## <a name="editing-a-tile-map"></a>Edita una asignación de icono

Para editar una asignación de icono, abra el archivo de .tmx en mosaico haciendo doble clic en el archivo .tmx o abriendo a través del menú archivo en mosaico. Moneda tiempo nivel icono mapas contienen tres capas: 

- **Entidades** : este nivel contiene iconos que se reemplazará con instancias de entidades en tiempo de ejecución. Algunos ejemplos son el Reproductor, monedas, enemigos y la puerta de la finalización de nivel.
- **Terreno** : este nivel contiene iconos que normalmente tienen colisión sólido. Colisión sólido permite que el Reproductor recorrer en estos iconos sin pasar por ella. Iconos con colisión sólido también pueden actuar como paredes y límites.
- **Fondo** : la capa de fondo contiene iconos que se utilizan como el fondo estático. Este nivel no se desplaza cuando se mueve la cámara en el nivel, creando así la apariencia de profundidad a través de parallax.

Como a continuación se explican más adelante, el código de nivel de carga espera estos tres capas en todos los niveles de tiempo de moneda.

### <a name="editing-terrain"></a>Edición de terreno
Se pueden colocar los iconos, haga clic en el **mastersheet** tileset y, a continuación, haga clic en el icono de mapa. Por ejemplo, para pintar el terreno nueva en un nivel:

1. Seleccione la capa de terreno
1. Haga clic en el icono para dibujar
1. Haga clic en o insertar y arrastre sobre el mapa para pintar el icono


    ![](cointime-images/image2.png "Haga clic en el icono para dibujar 1")

 

La parte superior izquierda de la tileset contiene todos del terreno en el tiempo de moneda. Terreno, que es sólido, incluye el **SolidCollision** propiedad, como se muestra en las propiedades del icono de la izquierda de la pantalla:

![](cointime-images/image3.png "Terreno, que es sólido, incluye la propiedad SolidCollision, tal como se muestra en las propiedades del icono de la izquierda de la pantalla")

### <a name="editing-entities"></a>Edición de entidades
Las entidades se pueden agregarse ni quitarse de un nivel: al igual que de terreno. El **mastersheet** tileset tiene todas las entidades que se coloca sobre la mitad de la distancia horizontal, por lo que puede que no sean visibles sin tener que desplazarse a la derecha:

![](cointime-images/image4.png "El tileset mastersheet tiene todas las entidades que se coloca sobre la mitad de la distancia horizontal, por lo que puede que no sean visibles sin tener que desplazarse a la derecha")

Nuevas entidades deben colocarse en el **entidades** capa.

![](cointime-images/image5.png "Nuevas entidades deben estar ubicadas en el nivel de entidades")

Busca el código de CoinTime el **EntityType** cuando se carga un nivel para identificar los iconos que se deben reemplazar por entidades: 

![](cointime-images/image6.png "Código de CoinTime busca el EntityType cuando se carga un nivel para identificar los iconos que se deben reemplazar por entidades")

Una vez que se ha modificado y guardado el archivo, los cambios automáticamente aparecerá si el proyecto se compila y ejecute:

![](cointime-images/image7.png "Una vez que se ha modificado y guardado el archivo, los cambios automáticamente se mostrarán si se compila el proyecto y ejecutar")


## <a name="adding-new-levels"></a>Agregar nuevos niveles

El proceso de agregar niveles a la hora de moneda requiere ningún cambio de código y solo de unos pequeños cambios en el proyecto. Para agregar un nuevo nivel:

1. Abra la carpeta de nivel se encuentra en <**CoinTime Root > \CoinTime\Assets\Content\levels**
1. Copie y pegue uno de los niveles, como **level0.tmx**
1. Cambiar el nombre del nuevo archivo .tmx por lo que sigue la secuencia de número de nivel con los niveles existentes, como **level8.tmx**
1. En Visual Studio o Visual Studio para Mac, agregar el nuevo archivo .tmx a la carpeta de niveles Android. Compruebe que el archivo utiliza el **AndroidAsset** acción de compilación.


    ![](cointime-images/image8.png "Compruebe que el archivo usa la acción de compilación AndroidAsset")


1. Agregue el nuevo archivo .tmx a la carpeta de niveles de iOS. Asegúrese de vincular el archivo desde su ubicación original y compruebe que usa el **BundleResource** acción de compilación.


    ![](cointime-images/image9.png "Asegúrese de vincular el archivo desde su ubicación original y compruebe que usa la acción de compilación BundleResource")


El nuevo nivel debe aparecer en la pantalla Seleccione nivel como nivel 9 (nombres de archivo de nivel empiezan en 0, pero los botones de nivel comienzan por el número 1):

![](cointime-images/image10.png "El nuevo nivel debe aparecer en la pantalla Seleccione nivel como level 9 empieza a nombres de nivel de archivo en 0, pero los botones de nivel comienzan por el número 1")


# <a name="level-loading"></a>Nivel de carga

Como se muestra anteriormente, nuevos niveles no requieren ningún cambio en el código: el juego detecta automáticamente los niveles si se denomina correctamente y se agregan a la **niveles** carpeta con la acción de compilación correcta (**BundleResource**o **AndroidAsset**).

La lógica para determinar el número de niveles que se encuentra en la `LevelManager` clase. Cuando una instancia de la `LevelManager` se construye (mediante el patrón singleton), el `DetermineAvailbleLevels` se llama al método:


```csharp
private void DetermineAvailableLevels()
{
    // This game relies on levels being named "levelx.tmx" where x is an integer beginning with
    // 1. We have to rely on MonoGame's TitleContainer which doesn't give us a GetFiles method - we simply
    // have to check if a file exists, and if we get an exception on the call then we know the file doesn't
    // exist. 
    NumberOfLevels = 0;
    while (true)
    {
        bool fileExists = false;
        try
        {
            using(var stream = TitleContainer.OpenStream("Content/levels/level" + NumberOfLevels + ".tmx"))
            {
            }
            // if we got here then the file exists!
            fileExists = true;
        }
        catch
        {
            // do nothing, fileExists will remain false
        }
        if (!fileExists)
        {
            break;
        }
        else
        {
            NumberOfLevels++;
        }
    }
}
```

CocosSharp no proporciona un enfoque multiplataforma para detectar si los archivos están presentes, por lo que se debe confiar en la `TitleContainer` clase para intentar abrir una secuencia. Si el código para abrir una secuencia que inicia una excepción y, a continuación, el archivo no existe y el bucle while saltos. Una vez que finalice el bucle, la `NumberOfLevels` propiedad notifica el número de niveles válido forman parte del proyecto.

El `LevelSelectScene` clase utiliza el `LevelManager.NumberOfLevels` para determinar cuántos botones para crear en el `CreateLevelButtons` método:


```csharp
private void CreateLevelButtons()
{
    const int buttonsPerPage = 6;
    int levelIndex0Based = buttonsPerPage * pageNumber;
    int maxLevelExclusive = System.Math.Min (levelIndex0Based + 6, LevelManager.Self.NumberOfLevels);
    int buttonIndex = 0;
    float centerX = this.ContentSize.Center.X;
    const float topRowOffsetFromCenter = 16;
    float topRowY = this.ContentSize.Center.Y + topRowOffsetFromCenter;
    for (int i = levelIndex0Based; i < maxLevelExclusive; i++)
    {
        ...
    }
}
```

El `NumberOflevels` propiedad se utiliza para determinar los botones que deben crearse. Este código considera que la página que el usuario está viendo actualmente y solo permite crear un máximo de seis botones por página. Haga clic en el botón instancias llamada la `HandleButtonClicked` método:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Este método asigna la `CurrentLevel` propiedad que se utiliza por la `GameScene` cuando se carga un nivel. Después de establecer el `CurrentLevel`, `GoToGameScene` método se genera, cambio de la escena de `LevelSelectScene` a `GameScene`.

El `GameScene` llamadas de constructor `GoToLevel`, que ejecuta la lógica de nivel de carga:


```csharp
private void GoToLevel(int levelNumber)
{
    LoadLevel (levelNumber);
    CreateCollision();
    ProcessTileProperties ();
    touchScreen = new TouchScreenInput(gameplayLayer);
    secondsLeft = secondsPerLevel;
}
```

A continuación echaremos un vistazo en métodos llamados en `GoToLevel`.


## <a name="loadlevel"></a>LoadLevel

El `LoadLevel` método es responsable de cargar el archivo .tmx y agregarla a la `GameScene`. Este método no crea ningún objeto interactivo como colisión o entidades: simplemente crea los objetos visuales para el nivel, también se denomina el *entorno*.


```csharp
private void LoadLevel(int levelNumber)
{
    currentLevel = new CCTileMap ("level" + levelNumber + ".tmx");
    currentLevel.Antialiased = false;
    backgroundLayer = currentLevel.LayerNamed ("Background");
    // CCTileMap is a CCLayer, so we'll just add it under all entities
    this.AddChild (currentLevel);
    // put the game layer after
    this.RemoveChild(gameplayLayer);
    this.AddChild(gameplayLayer);
    this.RemoveChild (hudLayer);
    this.AddChild (hudLayer);
}
```

El `CCTileMap` constructor toma un nombre de archivo, que se crea con el número de nivel que se pasa a `LoadLevel`. Para obtener más información sobre cómo crear y trabajar con `CCTileMap` instancias, vea el [utilizando coloca en mosaico con la Guía de CocosSharp](~/graphics-games/cocossharp/tiled.md).

Actualmente, CocosSharp no permite la reordenación de las capas sin quitar y volver a agregarlas a su elemento primario `CCScene` (que es el `GameScene` en este caso), por lo que las últimas líneas del método necesarios para volver a ordenar las capas.


## <a name="createcollision"></a>CreateCollision

El `CreateCollision` método construye una `LevelCollision` instancia que se usa para realizar *colisión sólido* entre el Reproductor y el entorno.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Sin esta colisión, el Reproductor se incluirían en el nivel y el juego sería reproducirse. Colisión sólido permite que el Reproductor guiará en el suelo y evita que el Reproductor de guía a través de las paredes o saltar seguridad a través de los límites máximos.

Colisión en el tiempo de moneda se puede agregar con ningún código adicional: únicas modificaciones a los archivos de mosaico. 


## <a name="processtileproperties"></a>ProcessTileProperties

Una vez que se carga un nivel y se crea el conflicto, `ProcessTileProperties` se llama para realizar la lógica basada en el icono Propiedades. Tiempo de moneda incluye un `PropertyLocation` struct para definir propiedades y las coordenadas del mosaico con estas propiedades:


```csharp
public struct PropertyLocation
{
    public CCTileMapLayer Layer;
    public CCTileMapCoordinates TileCoordinates;
    public float WorldX;
    public float WorldY;
    public Dictionary<string, string> Properties;
}
```

Esta estructura se usa para construir crear instancias de la entidad y quitar iconos innecesarios en el `ProcessTileProperties` método:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```

El bucle foreach evalúa cada propiedad de mosaico, comprobar si la clave sea `EntityType` o `RemoveMe`. `EntityType` indica que se debe crear una instancia de entidad. `RemoveMe` indica que el icono se debe quitar por completo en tiempo de ejecución.

Si una propiedad con el `EntityType` clave se encuentra, a continuación, `TryCreateEntity` se llama, los intentos de crear una entidad mediante la coincidencia con la propiedad la `EntityType` clave:


```csharp
private bool TryCreateEntity(string entityType, float worldX, float worldY)
{
    CCNode entityAsNode = null;
    switch (entityType)
    {
    case "Player":
        player = new Player ();
        entityAsNode = player;
        break;
    case "Coin":
        Coin coin = new Coin ();
        entityAsNode = coin;
        coins.Add (coin);
        break;
    case "Door":
        door = new Door ();
        entityAsNode = door;
        break;
    case "Spikes":
        var spikes = new Spikes ();
        this.damageDealers.Add (spikes);
        entityAsNode = spikes;
        break;
    case "Enemy":
        var enemy = new Enemy ();
        this.damageDealers.Add (enemy);
        this.enemies.Add (enemy);
        entityAsNode = enemy;
        break;
    }
    if(entityAsNode != null)
    {
        entityAsNode.PositionX = worldX;
        entityAsNode.PositionY = worldY;
        gameplayLayer.AddChild (entityAsNode);
    }
    return entityAsNode != null;
}
```


# <a name="adding-new-entities"></a>Agregar nuevas entidades

Tiempo de moneda utiliza el modelo de entidad para sus objetos del juego (descrita en la [guían de las entidades de CocosSharp](~/graphics-games/cocossharp/entities.md)). Todas las entidades que heredan de `CCNode`, lo que significa que se pueden agregar como elementos secundarios de la `gameplayLayer`.

Cada tipo de entidad también se hace referencia directamente a través de una lista o una sola instancia. Por ejemplo, el `Player` hace referencia a la `player` campo y todos los `Coin` instancias se hace referencia en una `coins` lista. Mantener las referencias directas a las entidades (en lugar de hacer referencia a ellas a través de la `gameLayer.Children` lista) se obtiene un código que tiene acceso a estas entidades más fáciles de leer y elimina la conversión de tipos potencialmente costosa.

El código existente proporciona una serie de tipos de entidad como ejemplos de cómo crear nuevas entidades. Los pasos siguientes se pueden utilizar para crear una nueva entidad:


## <a name="1---define-a-new-class-using-the-entity-pattern"></a>1: definir una nueva clase utilizando el modelo de entidad

El único requisito para la creación de una entidad es crear una clase que hereda de `CCNode`. Mayoría de las entidades tiene algún objeto visual, como un `CCSprite`, que se debe agregar como elemento secundario de la entidad en su constructor.

CoinTime proporciona el `AnimatedSpriteEntity` clase que simplifica la creación de entidades animadas. Las animaciones se tratarán con más detalle en la [sección animando entidades](#Animated_Entities).


## <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2: agregar una nueva entrada a la instrucción switch de TryCreateEntity

Instancias de la nueva entidad se deberían crear instancias en el `TryCreateEntity`. Si la entidad requiere la lógica de cada fotograma como colisión, AI o leer la entrada, la `GameScene` tiene que mantener una referencia al objeto. Si se necesitan varias instancias (como `Coin` o `Enemy` instancias), a continuación, un nuevo `List` deben agregarse a la `GameScene` clase.


## <a name="3--modify-tile-properties-for-the-new-entity"></a>3: modificar las propiedades de icono de la nueva entidad

Una vez que el código es compatible con la creación de la nueva entidad, debe agregarse a la tileset la nueva entidad. Se puede editar la tileset abriendo cualquier nivel `.tmx` archivo. 

El tileset se almacena de manera independiente desde el .tmx en el **mastersheet.tsx** de archivos, por lo que debe importarse antes de que se puede editar:

![](cointime-images/image11.png "El tileset se almacena de manera independiente desde el archivo .tsx, por lo que debe importarse antes de que se puede editar")

Una vez importado, propiedades de los iconos seleccionados son editables, y se puede agregar el EntityType:

![](cointime-images/image12.png "Una vez importado, propiedades de los iconos seleccionados son editables, y puede agregarse el EntityType")

Una vez creada la propiedad, su valor puede establecerse para que coincida con el nuevo `case` en `TryCreateEntity`:

![](cointime-images/image13.png "Una vez creada la propiedad, su valor puede establecerse para que coincida con el nuevo caso de TryCreateEntity")

Después de que se ha cambiado el tileset, debe exportarse: Esto hace que los cambios estén disponibles para todos los demás niveles:

![](cointime-images/image14.png "Después de que se ha cambiado el tileset, debe exportarse")

Debe sobrescribir el tileset existente **mastersheet.tsx** tileset:

![](cointime-images/image15.png "tileset debe sobrescribir el tileset mastersheet.tsx existente")


# <a name="entity-tile-removal"></a>Eliminación del icono de entidad

Cuando se carga un mapa de mosaico en un juego, los mosaicos individuales son objetos estáticos. Puesto que las entidades requieren un comportamiento personalizado, como un movimiento, código en tiempo de moneda quita mosaicos cuando se crean entidades.

`ProcessTileProperties` incluye la lógica para quitar los iconos que crean entidades mediante el `RemoveTile` método:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            ...
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        ...
    }
}
```

Esta eliminación automática de los iconos es suficiente para las entidades que ocupan un único mosaico en tileset, por ejemplo, monedas y enemigos. Entidades mayores requieren propiedades y lógica adicional.

La puerta de requiere dos iconos para dibujar completamente:

![](cointime-images/image16.png "La puerta de requiere dos iconos para dibujar por completo")

El icono de la parte inferior de la puerta contiene las propiedades para crear una entidad (**EntityType** establecido en **puerta**):

![](cointime-images/image17.png "El icono de la parte inferior de la puerta contiene las propiedades para crear un conjunto de entidades EntityType al puerta")

Dado que sólo el icono de la parte inferior de la puerta se quita cuando se crea la instancia de puerta, se necesita lógica adicional para quitar el icono superior en tiempo de ejecución. El icono superior tiene un **RemoveMe** propiedad establecida en **true**:

![](cointime-images/image18.png "El icono superior tiene una propiedad RemoveMe establecida en true")



Esta propiedad se utiliza para quitar iconos en `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        ...
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```


# <a name="entity-offsets"></a>Desplazamientos de entidad

Entidades creadas desde los iconos se sitúan alineando el centro de la entidad con el centro del mosaico. Las entidades de mayor, como `Door`, usar propiedades adicionales y la lógica para colocar correctamente. 

El icono de puerta de la parte inferior, que define la `Door` selección de ubicación de la entidad, especifica un **YOffset** valor 4. Sin esta propiedad, el `Door` instancia se coloca en el centro del mosaico:

![](cointime-images/image19.png "Sin esta propiedad, la instancia de la puerta se coloca en el centro del icono")

 

Esto se ha corregido aplicando la **YOffset** valor en `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            ...
        }
...
    }
}
```


# <a name="animated-entities"></a>Entidades animadas

Moneda tiempo incluye varias entidades animadas. El `Player` y `Enemy` entidades reproducción animaciones de recorrido y `Door` entidad desempeña una animación de apertura, una vez que se han recopilado todas las monedas.


## <a name="achx-files"></a>archivos de .achx

Las animaciones de tiempo de moneda se definen en archivos de .achx. Se define cada animación entre `AnimationChain` etiquetas, como se muestra en la siguiente animación definida en **propanimations.achx**:


```xml
<AnimationChain>
  <Name>Spikes</Name>
  <ColorKey>0</ColorKey>
  <Frame>
    <FlipHorizontal>false</FlipHorizontal>
    <FlipVertical>false</FlipVertical>
    <TextureName>..\images\mastersheet.png</TextureName>
    <FrameLength>0.1</FrameLength>
    <LeftCoordinate>1152</LeftCoordinate>
    <RightCoordinate>1168</RightCoordinate>
    <TopCoordinate>128</TopCoordinate>
    <BottomCoordinate>144</BottomCoordinate>
    <RelativeX>0</RelativeX>
    <RelativeY>0</RelativeY>
  </Frame>
</AnimationChain> 
```

Esta animación solo contiene un solo marco, lo que da lugar a la entidad de pico mostrar una imagen estática. Las entidades pueden utilizar archivos .achx si aparecen las animaciones de uno o varios marcos. Marcos adicionales pueden agregarse a los archivos de .achx sin necesidad de realizar cambios en el código. 

Marcos de definen qué imagen que se muestra en el `TextureName` parámetro y las coordenadas de la pantalla en el `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, y `BottomCoordinate` etiquetas. Estos representan las coordenadas de píxel del fotograma de animación en la imagen que se está usando: **mastersheet.png** en este caso.

![](cointime-images/image20.png "Estos representan las coordenadas de píxel del fotograma de animación en la imagen")

El `FrameLength` propiedad define el número de segundos que se debe mostrar un marco en una animación. Las animaciones de fotogramas solo pasar por alto este valor.

Se omiten todas las demás propiedades AnimationChain en el archivo .achx por tiempo de moneda.


## <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Lógica de la animación se encuentra en la `AnimatedSpriteEntity` (clase), que actúa como clase base para la mayoría de las entidades que se usa en el `GameScene`. Proporciona la funcionalidad siguiente:

 - La carga de `.achx` archivos
 - Caché de animación de animaciones cargadas
 - Instancia de CCSprite para mostrar la animación
 - Lógica para cambiar el marco actual

El constructor de picos proporciona un ejemplo sencillo de cómo cargar y usar animaciones:


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

El **propAnimations.achx** sólo contiene una animación, por lo que el constructor tiene acceso a esta animación por índice. Si un archivo .achx contiene varias animaciones, animaciones pueden hacer referencia a por su nombre, como se muestra en el `Enemy` constructor:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


# <a name="summary"></a>Resumen

Esta guía cubre los detalles de implementación de tiempo de moneda. Tiempo de moneda se crea para que sea un completo juego, pero también es un proyecto que se puede modificar fácilmente y se expanden. Agregar nuevos niveles y la creación de nuevas entidades para comprender mejor cómo se implementa el tiempo de moneda, se aconseja dedicar realizar las modificaciones del tiempo a niveles, los lectores.

## <a name="related-links"></a>Vínculos relacionados

- [Proyecto de juego (ejemplo)](https://developer.xamarin.com/samples/mobile/CoinTime/)
