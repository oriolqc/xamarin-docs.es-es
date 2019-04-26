---
title: Detalles de tiempo de juego de moneda
description: Esta guía describen los detalles de implementación en el juego de tiempo de moneda, como trabajar con mapas de mosaico, creación de entidades, animación de sprites y colisión eficaz de implementar.
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: af914e10eb93aa0668743a113ffe647a939fea75
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61395956"
---
# <a name="coin-time-game-details"></a>Detalles de tiempo de juego de moneda

_Esta guía describen los detalles de implementación en el juego de tiempo de moneda, como trabajar con mapas de mosaico, creación de entidades, animación de sprites y colisión eficaz de implementar._

Tiempo de moneda es una completa plataforma de juego para iOS y Android. El objetivo del juego consiste en recopilar todos de monedas en un nivel y, a continuación, llegar a la puerta de salida al tiempo que evita los enemigos y obstáculos.

![](cointime-images/image1.png "El objetivo del juego consiste en recopilar todos de monedas en un nivel y, a continuación, llegar a la puerta de salida al tiempo que evita los enemigos y obstáculos")

Esta guía describe los detalles de implementación en tiempo de moneda, que abarcan los siguientes temas:

- [Trabajar con archivos tmx](#working-with-tmx-files)
- [Nivel de carga](#level-loading)
- [Adición de nuevas entidades](#adding-new-entities)
- [Entidades animadas](#animated-entities)


## <a name="content-in-coin-time"></a>Contenido en tiempo de moneda

Tiempo de moneda es un proyecto de ejemplo que representa cómo puede estar organizado de un proyecto CocosSharp completo. Moneda del tiempo estructura pretende simplificar la incorporación y el mantenimiento de contenido. Lo usa **.tmx** archivos creados por [mosaico](http://www.mapeditor.org) para los niveles y los archivos XML para definir animaciones. Modificar o agregar el nuevo contenido se puede lograr con el mínimo esfuerzo. 

Aunque este enfoque hace tiempo moneda un proyecto de efectivo para aprendizaje y experimentación, también refleja juegos profesionales con cómo se realizan. Esta guía explica algunos de los enfoques adoptados para simplificar la adición y modificación de contenido.


## <a name="working-with-tmx-files"></a>Trabajar con archivos tmx

Niveles de tiempo de moneda se definen mediante el formato de archivo .tmx, que es generado por el [mosaico](http://www.mapeditor.org) icono editor de mapa. Para obtener una explicación detallada de cómo trabajar con el mosaico, vea el [uso de Tiled con Cocos Sharp guía](~/graphics-games/cocossharp/tiled.md). 

Cada nivel se define en su propio archivo .tmx contenido en el **CoinTime/activos o contenido/niveles** carpeta. Todos los niveles de tiempo de moneda compartan un archivo tileset, que se define en el **mastersheet.tsx** archivo. Este archivo define las propiedades personalizadas de cada icono, por ejemplo, si el icono tiene una colisión sólida o si el icono se debe reemplazar por una instancia de entidad. El archivo mastersheet.tsx permite que las propiedades que se define una sola vez y se utiliza en todos los niveles. 


### <a name="editing-a-tile-map"></a>Edita una asignación de icono

Para editar un icono de mapa, abra el archivo de .tmx en mosaico mediante doble clic en el archivo .tmx o si lo abre a través del menú archivo en mosaico. Coin Time icono nivel mapas contienen tres capas: 

- **Las entidades** : este nivel contiene los iconos que se reemplazará con instancias de entidades en tiempo de ejecución. Algunos ejemplos son el Reproductor, monedas, enemigos y la puerta de final de nivel.
- **Terreno** : este nivel contiene iconos que normalmente tienen colisión sólido. Colisión sólido permite que el Reproductor recorrer en estos iconos sin pasar por ella. Los iconos con colisiones sólido también pueden actuar como paredes y techos.
- **En segundo plano** : la capa de fondo contiene iconos que se utilizan como el fondo estático. Esta capa no se desplaza cuando se mueve la cámara a lo largo del nivel, crear el aspecto de profundidad a través de paralaje.

Como se analiza más adelante, el código de nivel de carga espera estas tres capas en todos los niveles de tiempo de moneda.

#### <a name="editing-terrain"></a>Edición de terreno

Se pueden colocar los iconos, haga clic en el **mastersheet** tileset y, a continuación, haga clic en el icono de mapa. Por ejemplo, para pintar el terreno nuevo en un nivel:

1. Seleccione la capa de terreno
1. Haga clic en el icono para dibujar
1. Haga clic en o insertar y arrastre el mapa para pintar el icono

    ![](cointime-images/image2.png "Haga clic en el icono que se va a dibujar 1")

La parte superior izquierda de la tileset contiene todos del terreno en el tiempo de moneda. Terreno, que es sólido, incluye el **SolidCollision** propiedad, como se muestra en las propiedades del icono de la izquierda de la pantalla:

![](cointime-images/image3.png "Terreno, que es sólido, incluye la propiedad SolidCollision, tal como se muestra en las propiedades del icono de la izquierda de la pantalla")

#### <a name="editing-entities"></a>Editar entidades

Las entidades se pueden agregar o quitar de un nivel: igual que el terreno. El **mastersheet** tileset tiene todas las entidades que se coloca sobre mitad horizontalmente, por lo que quizá no sean visibles sin necesidad de desplazarse a la derecha:

![](cointime-images/image4.png "El tileset mastersheet tiene todas las entidades que se coloca sobre mitad horizontalmente, por lo que quizá no sean visibles sin necesidad de desplazarse a la derecha")

Nuevas entidades se deben colocar en el **entidades** capa.

![](cointime-images/image5.png "Nuevas entidades se deben colocar en la capa de entidades")

Código de CoinTime busca el **EntityType** cuando se carga un nivel para identificar los iconos que se deben reemplazar por las entidades: 

![](cointime-images/image6.png "Código de CoinTime busca el EntityType cuando se carga un nivel para identificar los iconos que se deben reemplazar por las entidades")

Una vez que se ha modificado y guardado el archivo, los cambios aparecerán automáticamente si se compila el proyecto y ejecutar:

![](cointime-images/image7.png "Una vez que se ha modificado y guardado el archivo, los cambios aparecerán automáticamente si se compila el proyecto y ejecutar")


### <a name="adding-new-levels"></a>Agregar nuevos niveles

El proceso de agregar niveles a la hora de moneda requiere ningún cambio de código y solo algunos pequeños cambios para el proyecto. Para agregar un nuevo nivel:

1. Abra la carpeta de nivel se encuentra en <**CoinTime Root > \CoinTime\Assets\Content\levels**
1. Copie y pegue uno de los niveles, como **level0.tmx**
1. Cambiar el nombre del nuevo archivo .tmx por lo que sigue la secuencia de números nivel con los niveles existentes, tales como **level8.tmx**
1. En Visual Studio o Visual Studio para Mac, agregue el nuevo archivo .tmx a la carpeta de los niveles de Android. Compruebe que el archivo utiliza el **AndroidAsset** acción de compilación.

    ![](cointime-images/image8.png "Compruebe que el archivo usa la acción de compilación AndroidAsset")

1. Agregue el nuevo archivo .tmx a la carpeta de los niveles de iOS. Asegúrese de vincular el archivo desde su ubicación original y compruebe que usa el **BundleResource** acción de compilación.

    ![](cointime-images/image9.png "Asegúrese de vincular el archivo desde su ubicación original y compruebe que usa la acción de compilación BundleResource")

El nuevo objeto level debe aparecer en la pantalla Seleccione nivel como nivel 9 (nombres de archivo de nivel empiezan en 0, pero los botones de nivel comienzan con el número 1):

![](cointime-images/image10.png "El nuevo objeto level debe aparecer en la pantalla Seleccione nivel como level 9 empieza a nombres de nivel de archivo en 0, pero los botones de nivel comienzan con el número 1")


## <a name="level-loading"></a>Nivel de carga

Como se muestra anteriormente, nuevos niveles no requieren ningún cambio en el código: el juego detecta automáticamente los niveles si se denominado correctamente y se agrega a la **niveles** carpeta con la acción de compilación correcta (**BundleResource**o **AndroidAsset**).

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

CocosSharp no proporciona un enfoque multiplataforma para detectar si los archivos están presentes, por lo que tenemos que depender de la `TitleContainer` clase para intentar abrir una secuencia. Si el código para abrir una secuencia que inicia una excepción y, a continuación, el archivo no existe y el bucle while saltos. Una vez que finalice el bucle, la `NumberOfLevels` propiedad informa de cuántos niveles válidos forman parte del proyecto.

El `LevelSelectScene` clase utiliza la `LevelManager.NumberOfLevels` para determinar cuántos botones para crear en el `CreateLevelButtons` método:


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

El `NumberOflevels` propiedad se utiliza para determinar qué botones se deben crear. Este código considera que la página que el usuario está viendo actualmente y solo crea un máximo de seis botones por página. Haga clic en el botón instancias llamada la `HandleButtonClicked` método:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Este método asigna el `CurrentLevel` propiedad que está usando el `GameScene` al cargar un nivel. Después de establecer el `CurrentLevel`, `GoToGameScene` se genera el método, cambio de la escena `LevelSelectScene` a `GameScene`.

El `GameScene` constructor llama a `GoToLevel`, que lleva a cabo la lógica de nivel de carga:


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

A continuación le echamos un vistazo a los métodos llamados en `GoToLevel`.


### <a name="loadlevel"></a>LoadLevel

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

El `CCTileMap` constructor toma un nombre de archivo, se crea con el número de nivel pasado a `LoadLevel`. Para obtener más información sobre cómo crear y trabajar con `CCTileMap` instancias, vea el [uso de Tiled con CocosSharp guía](~/graphics-games/cocossharp/tiled.md).

Actualmente, CocosSharp no permite la reordenación de las capas sin quitar y volver a agregarlas a su elemento principal `CCScene` (que es el `GameScene` en este caso), por lo que las últimas líneas del método son necesarios para cambiar el orden de las capas.


### <a name="createcollision"></a>CreateCollision

El `CreateCollision` método construye una `LevelCollision` instancia que se usa para realizar *colisión sólido* entre el Reproductor y el entorno.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Sin este conflicto, el Reproductor podría pasar explícitamente el nivel y sería reproduzca el juego. Colisión sólido permite que el Reproductor guiará en el suelo y se impide que el Reproductor a través de las paredes o saltar copia a través de los límites máximos.

Colisión en tiempo de moneda se puede agregar con ningún código adicional: solo las modificaciones a los archivos en mosaico. 


### <a name="processtileproperties"></a>ProcessTileProperties

Una vez que se carga un nivel y se crea la colisión, `ProcessTileProperties` se llama para realizar la lógica basada en Propiedades del mosaico. Moneda incluye un `PropertyLocation` struct para definir las propiedades y las coordenadas del mosaico con estas propiedades:


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

Esta estructura se usa para construir instancias de entidad de crear y quitar iconos innecesarios en el `ProcessTileProperties` método:


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

El bucle foreach evalúa cada propiedad de icono, comprobando si la clave `EntityType` o `RemoveMe`. `EntityType` indica que se debe crear una instancia de entidad. `RemoveMe` indica que el icono se debe quitar por completo en tiempo de ejecución.

Si una propiedad con el `EntityType` clave se encuentra, a continuación, `TryCreateEntity` se llama, que intenta crear una entidad mediante la propiedad que coincide con el `EntityType` clave:


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


## <a name="adding-new-entities"></a>Adición de nuevas entidades

Tiempo de moneda utiliza el modelo de entidad para sus objetos del juego (descrita en el [guiar las entidades de CocosSharp](~/graphics-games/cocossharp/entities.md)). Todas las entidades que heredan de `CCNode`, lo que significa que se pueden agregar como elementos secundarios de la `gameplayLayer`.

Cada tipo de entidad también se hace referencia directamente a través de una lista o una sola instancia. Por ejemplo, el `Player` hace referencia el `player` campo y todos `Coin` instancias se hace referencia en una `coins` lista. Mantener las referencias directas a las entidades (en lugar de hacer referencia a ellas a través de la `gameLayer.Children` lista) hace que el código que tiene acceso a estas entidades más fáciles de leer y elimina la conversión de tipos potencialmente costosa.

El código existente proporciona una serie de tipos de entidad como ejemplos de cómo crear nuevas entidades. Los siguientes pasos pueden utilizarse para crear una nueva entidad:


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1: definir una nueva clase utilizando el modelo de entidad

El único requisito para crear una entidad es crear una clase que hereda de `CCNode`. La mayoría de las entidades tienen algún objeto visual, como un `CCSprite`, que se debe agregar como elemento secundario de la entidad en su constructor.

CoinTime proporciona el `AnimatedSpriteEntity` clase lo que simplifica la creación de entidades animadas. Las animaciones se tratarán con más detalle en la [sección entidades animado](#animated-entities).


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2: agregar una nueva entrada a la instrucción switch TryCreateEntity

Las instancias de la nueva entidad deben crearse en el `TryCreateEntity`. Si la entidad requiere una lógica de cada fotograma como colisión, inteligencia artificial o leer la entrada, el `GameScene` necesita mantener una referencia al objeto. Si se necesitan varias instancias (como `Coin` o `Enemy` instancias), a continuación, un nuevo `List` deben agregarse a la `GameScene` clase.


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3: modificar las propiedades de icono para la nueva entidad

Una vez que el código es compatible con la creación de la nueva entidad, debe agregarse a la tileset la nueva entidad. Se puede editar el tileset abriendo cualquier nivel `.tmx` archivo. 

El tileset se almacena independientes de la .tmx en el **mastersheet.tsx** de archivos, por lo que debe importarse antes de se puede editar:

![](cointime-images/image11.png "Se almacena el tileset independiente desde el archivo .tsx, por lo que debe importarse antes de se puede editar")

Una vez importado, las propiedades de los iconos seleccionados son editables, y puede agregarse el EntityType:

![](cointime-images/image12.png "Una vez importado, las propiedades de los iconos seleccionados son editables, y se puede agregar el EntityType")

Una vez creada la propiedad, su valor se puede establecer para que coincida con el nuevo `case` en `TryCreateEntity`:

![](cointime-images/image13.png "Una vez creada la propiedad, su valor se puede establecer para que coincida con el nuevo caso de TryCreateEntity")

Una vez modificado el tileset, debe exportarse: Esto hace que los cambios que estén disponibles para todos los demás niveles:

![](cointime-images/image14.png "Una vez modificado el tileset, debe exportarse")

El tileset debe sobrescribir la existente **mastersheet.tsx** tileset:

![](cointime-images/image15.png "tileset debe sobrescribir la existente tileset mastersheet.tsx")


## <a name="entity-tile-removal"></a>Eliminación del icono de entidad

Cuando se carga un mapa de mosaico en un juego, los iconos individuales son objetos estáticos. Puesto que las entidades requieren un comportamiento personalizado, como un movimiento, código en tiempo de moneda quita mosaicos cuando se crean las entidades.

`ProcessTileProperties` incluye la lógica para quitar los iconos que creación entidades con el `RemoveTile` método:


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

Esta eliminación automática de iconos es suficiente para las entidades que ocupan un solo mosaico en tileset, por ejemplo, monedas y enemigos. Entidades mayores requieren las propiedades y lógica adicional.

La puerta requiere dos iconos para dibujar completamente:

![](cointime-images/image16.png "La puerta requiere dos iconos para dibujar completamente")

El icono de la parte inferior de la puerta contiene las propiedades para la creación de una entidad (**EntityType** establecido en **puerta**):

![](cointime-images/image17.png "El icono de la parte inferior de la puerta contiene las propiedades para crear un conjunto de entidades EntityType al puerta")

Puesto que sólo el icono de la parte inferior en la puerta se quita cuando se crea la instancia de la puerta, se necesita lógica adicional para quitar el icono superior en tiempo de ejecución. El icono superior tiene un **RemoveMe** propiedad establecida en **true**:

![](cointime-images/image18.png "El icono superior tiene una propiedad RemoveMe establecida en true")



Esta propiedad se usa para quitar los iconos de `ProcessTileProperties`:


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


## <a name="entity-offsets"></a>Desplazamientos de entidad

Creado a partir de los iconos de las entidades se colocan alineando el centro de la entidad con el centro del icono. Las entidades de mayor tamaño, como `Door`, use propiedades adicionales y la lógica para colocar correctamente. 

El icono de puerta de la parte inferior, que define el `Door` selección de ubicación de la entidad, especifica un **YOffset** valor 4. Sin esta propiedad, el `Door` instancia se coloca en el centro del icono:

![](cointime-images/image19.png "Sin esta propiedad, la instancia de la puerta se coloca en el centro del icono")

 

Corrija este problema aplicando el **YOffset** valor en `ProcessTileProperties`:


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


## <a name="animated-entities"></a>Entidades animadas

Moneda incluye varias entidades animadas. El `Player` y `Enemy` entidades reproducción animaciones de recorrido y `Door` entidad desempeña una animación de apertura de una vez que se hayan recopilado todas las monedas.


### <a name="achx-files"></a>archivos .achx

Las animaciones de tiempo de moneda se definen en archivos .achx. Cada animación se define entre `AnimationChain` etiquetas, como se muestra en la siguiente animación definida en **propanimations.achx**:


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

Esta animación solo contiene un solo fotograma, lo que resulta en la entidad de pico mostrar una imagen estática. Las entidades pueden utilizar archivos .achx si se muestran las animaciones de uno o varios fotogramas. Marcos adicionales se pueden agregar a los archivos .achx sin necesidad de realizar cambios en el código. 

Marcos de definen qué imagen se muestre en el `TextureName` parámetro y las coordenadas de la pantalla en la `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, y `BottomCoordinate` etiquetas. Estos representan las coordenadas de píxel del fotograma de animación en la imagen que se usa – **mastersheet.png** en este caso.

![](cointime-images/image20.png "Estos representan las coordenadas de píxel del fotograma de animación en la imagen")

El `FrameLength` propiedad define el número de segundos que debe mostrarse un fotograma de una animación. Las animaciones de fotogramas solo pasar por alto este valor.

Se omiten todas las demás propiedades AnimationChain en el archivo .achx por hora de moneda.


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Lógica de animación se incluye en el `AnimatedSpriteEntity` (clase), que actúa como clase base para la mayoría de las entidades que se usa en el `GameScene`. Proporciona la funcionalidad siguiente:

 - La carga de `.achx` archivos
 - Caché de animación de animaciones cargadas
 - Instancia de CCSprite para mostrar la animación
 - Lógica para cambiar el marco actual

El constructor de picos proporciona un ejemplo sencillo de cómo cargar y utilizar animaciones:


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

El **propAnimations.achx** contiene sólo una animación, por lo que el constructor tiene acceso a esta animación por índice. Si un archivo .achx contiene varias animaciones, a continuación, se pueden hacer referencia en las animaciones por nombre, como se muestra en el `Enemy` constructor:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>Resumen

Esta guía cubre los detalles de implementación de tiempo de moneda. Tiempo de moneda se crea un juego completo, pero también es un proyecto que se puede modificar fácilmente y se expanden. Se anima al lector dedicar realice modificaciones de tiempo en niveles, agregar nuevos niveles y crear nuevas entidades para comprender mejor cómo se implementa el tiempo de moneda.

## <a name="related-links"></a>Vínculos relacionados

- [Proyecto de juego (ejemplo)](https://developer.xamarin.com/samples/mobile/CoinTime/)
