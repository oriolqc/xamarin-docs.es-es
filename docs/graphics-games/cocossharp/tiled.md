---
title: Uso de mosaico con CocosSharp
description: Mosaico es una eficaz, flexible y su aplicación es para la creación de mosaico ortogonal e isométrica se asigna para juegos. CocosSharp proporciona integración incorporada para el formato de archivo nativo del mosaico.
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 8a7782097324829b8150b968c5658a864d1fab4a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921305"
---
# <a name="using-tiled-with-cocossharp"></a>Uso de mosaico con CocosSharp

_Mosaico es una eficaz, flexible y su aplicación es para la creación de mosaico ortogonal e isométrica se asigna para juegos. CocosSharp proporciona integración incorporada para el formato de archivo nativo del mosaico._

El *mosaico* aplicación es un estándar para la creación de *icono mapas* para su uso en el desarrollo de juegos. Esta guía le guiará por el proceso de tomar un archivo de .tmx existente (archivo creado por mosaico) y usarlo en un juego de CocosSharp. En concreto, esta guía se tratará:

 - El propósito de mapas de mosaico
 - Trabajar con archivos de .tmx
 - Consideraciones para presentar imágenes de píxeles
 - Usar propiedades de mosaico en tiempo de ejecución

Cuando termine de tenemos la demostración siguiente:

![](tiled-images/image1.png "La aplicación de demostración que creó siguiendo los pasos descritos en esta guía")


## <a name="the-purpose-of-tile-maps"></a>El propósito de mapas de mosaico

Icono mapas han existido en el desarrollo de juego de décadas, pero todavía suelen utilizarse en juegos 2D por su eficacia y esthetics. Icono mapas son lograr un nivel muy alto de eficacia a través de su uso de conjuntos de mosaico: la imagen de origen usa mosaico se asigna. Un conjunto de mosaico es una colección de imágenes que se combinan en un archivo. Aunque icono conjuntos hacen referencia a imágenes que se utilizan en las asignaciones de mosaico, archivos que contienen varias imágenes más pequeñas también se denominan hojas sprite o sprite se asigna en el desarrollo de juego. Podemos visualizar cómo se utilizan conjuntos de mosaico mediante la adición de una cuadrícula en el conjunto de icono que se usará en nuestra demostración:

![](tiled-images/image2.png "Una vista visualizada de cómo se utilizan conjuntos de mosaico mediante la adición de que una cuadrícula en el icono del conjunto que se usará en la demostración")

Mapas de mosaico organizan los iconos de mosaico conjuntos individuales. Debemos mencionar que cada asignación de icono no es necesario almacenar su propia copia del icono establecer – en su lugar, varias asignaciones de mosaico pueden hacer referencia el mismo conjunto de mosaico. Esto significa que, además del conjunto de mosaico, mapas de mosaico requieren muy poca memoria. Esto permite la creación de un gran número de asignaciones de mosaico, incluso cuando se usan para crear un área de juego grandes, como un [desplazamiento reformador al platino](http://en.wikipedia.org/wiki/Platform_game) entorno. A continuación muestra con el mismo conjunto de mosaico de organizaciones posibles:

![](tiled-images/image3.png "Esta imagen muestra organizaciones posibles con el mismo conjunto de mosaico")

![](tiled-images/image4.png "Esta imagen muestra organizaciones posibles con el mismo conjunto de mosaico")


## <a name="working-with-tmx-files"></a>Trabajar con archivos de .tmx

El formato de archivo .tmx es un archivo XML creado por la aplicación de mosaico, que puede ser [descargar de forma gratuita en el sitio Web de mosaico](http://www.mapeditor.org/). El formato de archivo .tmx almacena la información de asignaciones de mosaico. Normalmente, un juego tendrá un archivo de .tmx para cada área de nivel o en otra diferente.

Esta guía se centra en cómo usar archivos .tmx existentes en CocosSharp; Sin embargo, tutoriales adicionales pueden encontrarse en línea, incluidos los [esta introducción al editor de mapa de mosaico](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

Debe descomprimir el [archivo zip contenido](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) para usarlo en el juego. Lo primero que tener en cuenta es que los mapas de mosaico usan el archivo de .tmx (dungeon.tmx), así como uno o más archivos de imagen que definen el icono establecer datos (dungeon_1.png). El juego necesita que se incluyan ambos archivos para cargar el .tmx en tiempo de ejecución, por lo que agregar al proyecto **contenido** carpeta (que se encuentra en la **activos** carpeta en los proyectos de Android). En concreto, agregar los archivos a una carpeta denominada **tilemaps** dentro de la **contenido** carpeta:

![](tiled-images/image5.png "Agregar los archivos a una carpeta denominada tilemaps dentro de la carpeta de contenido")

El **dungeon.tmx** ahora se puede cargar el archivo en tiempo de ejecución en un `CCTileMap` objeto. A continuación, modifique la `GameLayer` (u objeto de contenedor raíz equivalente) para que contenga un `CCTileMap` instancia y para agregarlo como elemento secundario:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Si se ejecuta el juego se verá el mapa de icono aparece en la esquina inferior izquierda de la pantalla:

![](tiled-images/image6.png "Si el juego se ejecuta, el mapa de icono aparece en la esquina inferior izquierda de la pantalla")


## <a name="considerations-for-rendering-pixel-art"></a>Consideraciones para presentar imágenes de píxeles

Imágenes de píxeles, en el contexto de desarrollo de juegos de vídeo, hace referencia a 2D arte visual que se crea normalmente por mano y suele ser una resolución baja. Imágenes de píxeles pueden ser de manera restrictiva intensivas para crear, por lo que conjuntos de mosaico de píxel prediseñadas incluyen a menudo iconos de baja resolución, como 16 o 32 alto y el ancho de píxel de tiempo. Si no se escala en tiempo de ejecución, a menudo es demasiado pequeño para tabletas y teléfonos más modernos carátulas de píxeles.

Podemos ajustar dimensiones mostradas en archivo de GameAppDelegate.cs nuestro del juego, donde agregaremos una llamada a `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Para obtener más información sobre `CCSceneResolutionPolicy`, consulte nuestra guía en [control resoluciones en CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Si se ejecuta el juego ahora, veremos el juego ocupan la pantalla completa de nuestro dispositivo:

![](tiled-images/image7.png "El juego ocupan la pantalla completa del dispositivo")

Por último, queremos deshabilitar suavizado de contorno en nuestro mapa del mosaico. El `Antialiased` propiedad aplica un efecto Desenfoque al representar los objetos que se ha aplicado el zoom en. Suavizado de contorno puede resultar útil para reducir la apariencia de pixelado de objetos gráficos, pero también puede introducir sus propios artefactos de representación. En concreto, el suavizado de contorno desenfoca el contenido de cada mosaico. Sin embargo, los bordes de cada mosaico no están borrosas, lo que hace que los mosaicos individuales se resaltan en lugar de mezcla con iconos adyacentes. También debemos mencionar que juegos de imágenes de píxeles a menudo preservar su apariencia pixelado para mantener un *los* sentirse.

Establecer `Antialiased` a `false` después de crear el `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

Ahora nuestro mapa de mosaico no aparecerá borrosa:

![](tiled-images/image8.png "Ahora el mapa de mosaico no aparecerá borroso")


## <a name="using-tile-properties-at-runtime"></a>Utilizando el icono Propiedades en tiempo de ejecución

Hasta ahora tenemos un `CCTileMap` cargar un archivo .tmx y mostrar, pero no tenemos ninguna manera de interactuar con él. En concreto, determinados iconos (por ejemplo, nuestro Tesoro) necesitan tener lógica personalizada. Se podrá ejecutar paso a paso cómo detectar propiedades del mosaico personalizado y reaccionar a estas propiedades una vez identificadas en tiempo de ejecución de diversas maneras.

Antes de que se escriba ningún código, necesitamos agregar propiedades a nuestro mapa del mosaico a través de mosaico. Para ello, abra el archivo dungeon.tmx en el programa de mosaico. Asegúrese de abrir el archivo que se está usando en el proyecto de juego.

Una vez abierto, seleccione el Tesoro en el icono que se establece para ver sus propiedades:

![](tiled-images/image9.png "Una vez abierto, seleccione el Tesoro en el icono que se establece para ver sus propiedades")

Si las propiedades de pecho Tesoro no aparece, haga doble clic en el Tesoro y seleccione **propiedades del mosaico**:

![](tiled-images/image10.png "Si las propiedades de pecho Tesoro no aparece, haga doble clic en el Tesoro y seleccione Propiedades de mosaico")

Propiedades del mosaico se implementan con un nombre y un valor. Para agregar una propiedad, haga clic en el **+** botón, escriba el nombre **IsTreasure**, haga clic en **Aceptar**, a continuación, escriba el valor **true**: 

![](tiled-images/image11.png "Para agregar una propiedad, haga clic en el botón, escriba el nombre IsTreasure, haga clic en Aceptar y después escriba el valor true")

No olvide guardar el archivo .tmx después de modificar las propiedades.

Por último, vamos a agregar código para buscar la propiedad agregadas recientemente. Se repetirá por cada `CCTileMapLayer` (nuestro mapa tiene 2 niveles), a continuación, a través de cada fila y columna que se va a buscar los mosaicos que tienen el `IsTreasure` propiedad:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

La mayoría del código se explican por sí mismas, pero debemos hablar sobre el control de iconos del Tesoro. En este caso la Estamos quitando los iconos que se identifican como cofres. Esto es porque cofres probablemente, necesitará código personalizado en tiempo de ejecución a una colisión con efecto y a conceder al Reproductor el contenido de la Tesoro cuando se abre. Además, el Tesoro necesite reaccionar frente a la que se va a abrir (y cambiar su apariencia visual) y puede tener lógica sobre solo aparecen cuando todos los que aparecen en pantalla enemigos han rechace el.

En otras palabras, se beneficiará del Tesoro en una entidad en lugar de que se va a un mosaico simple en el `CCTileMap`. Para obtener más información sobre las entidades de juegos, consulte la [guían de entidades en CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="summary"></a>Resumen

Este tutorial describe cómo cargar archivos de .tmx creados por el mosaico en una aplicación CocosSharp. Muestra cómo modificar la resolución de la aplicación para tener en cuenta las carátulas de píxel de menor resolución y cómo encontrar iconos por sus propiedades para realizar lógica personalizada, como la creación de instancias de entidad.

## <a name="related-links"></a>Vínculos relacionados

- [Sitio Web de mosaico](http://www.mapeditor.org/)
- [Zip contenido](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
