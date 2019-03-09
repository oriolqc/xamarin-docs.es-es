---
title: Uso de Tiled con CocosSharp
description: Mosaico es una solución eficaz, flexible y asigna su aplicación es para la creación de icono ortogonal e isométrica para juegos. CocosSharp proporciona integración incorporada para formato de archivo nativo del mosaico.
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 8e7ef890af264bb08827d86c635d555184f1ec00
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672513"
---
# <a name="using-tiled-with-cocossharp"></a>Uso de Tiled con CocosSharp

_Mosaico es una solución eficaz, flexible y asigna su aplicación es para la creación de icono ortogonal e isométrica para juegos. CocosSharp proporciona integración incorporada para formato de archivo nativo del mosaico._

El *mosaico* aplicación es un estándar para la creación de *icono mapas* para su uso en desarrollo de juegos. Esta guía le guiará a través de cómo tomar un archivo .tmx existente (archivo creado por mosaico) y usarlo en un juego de CocosSharp. En concreto, esta guía se tratará:

 - El propósito de mapas de icono
 - Trabajar con archivos .tmx
 - Consideraciones para representar el arte de píxeles
 - Uso de propiedades de los iconos en tiempo de ejecución

Cuando termine, tendrá la demostración siguiente:

![](tiled-images/image1.png "La aplicación de demostración creada siguiendo los pasos descritos en esta guía")


## <a name="the-purpose-of-tile-maps"></a>El propósito de mapas de icono

Mapas de mosaico han existido en el desarrollo de juegos durante décadas, pero todavía utilizan normalmente en juegos en 2D su eficacia y esthetics. Icono mapas son capaz de lograr un nivel muy alto de la eficacia mediante el uso de conjuntos de icono: la imagen de origen utilizado por los mapas de icono. Un conjunto de mosaico es una colección de imágenes que se combinan en un archivo. Aunque los conjuntos de mosaico, consulte imágenes utilizadas en el icono de mapas, los archivos que contienen varias imágenes más pequeñas también se denominan hojas sprite o sprite se asigna en el desarrollo de juegos. Podemos visualizar cómo se usan los conjuntos de mosaico mediante la adición de una cuadrícula para el conjunto de fichas que vamos a usar en nuestra demostración:

![](tiled-images/image2.png "Una vista visualizada de cómo se utilizan conjuntos de mosaico mediante la adición de que una cuadrícula en el icono del conjunto que se usará en la demostración")

Icono mapas organizan los iconos de mosaico conjuntos individuales. Debemos mencionar que cada asignación de icono no se necesita almacenar su propia copia del icono set – en su lugar, varios mapas de mosaico pueden hacer referencia el mismo conjunto de icono. Esto significa que, además del conjunto de mosaico, mapas de icono requieren muy poca memoria. Esto permite la creación de un gran número de asignaciones de mosaico, incluso cuando se usan para crear un área de juego grandes, como un [desplazamiento plataformas](https://en.wikipedia.org/wiki/Platform_game) entorno. A continuación muestra con el mismo conjunto de mosaico de organizaciones posibles:

![](tiled-images/image3.png "Esta imagen muestra organizaciones posibles con el mismo conjunto de icono")

![](tiled-images/image4.png "Esta imagen muestra organizaciones posibles con el mismo conjunto de icono")


## <a name="working-with-tmx-files"></a>Trabajar con archivos .tmx

El formato de archivo .tmx es un archivo XML creado por la aplicación de mosaico, que puede ser [descargar gratuitamente en el sitio Web de mosaico](http://www.mapeditor.org/). El formato de archivo .tmx almacena la información de asignaciones de icono. Normalmente, un juego tendrá un archivo .tmx para cada área independiente o nivel.

Esta guía se centra en cómo usar archivos .tmx existentes en CocosSharp; Sin embargo, otros tutoriales pueden encontrarse en línea, incluido [esta introducción al editor de mapa de mosaico](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

Tendrá que descomprimir los [archivo zip contenido](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) para usarlo en nuestro juego. Lo primero que tener en cuenta es que icono uso de mapas del archivo de .tmx (dungeon.tmx), así como uno o más archivos de imagen que definen el icono de establecen los datos (dungeon_1.png). Por lo tanto, el juego necesita que se incluyan ambos archivos para cargar el .tmx en tiempo de ejecución, agregue tanto para el proyecto **contenido** carpeta (que está incluido en el **activos** carpetas en proyectos de Android). En concreto, agregue los archivos en una carpeta llamada **tilemaps** dentro de la **contenido** carpeta:

![](tiled-images/image5.png "Agregue los archivos a una carpeta denominada tilemaps dentro de la carpeta de contenido")

El **dungeon.tmx** ahora se puede cargar el archivo en tiempo de ejecución en un `CCTileMap` objeto. A continuación, modifique la `GameLayer` (o el objeto contenedor raíz equivalente) para que contenga un `CCTileMap` instancia y agregarlo como un elemento secundario:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Si se ejecuta el juego se verá el icono de mapa aparecen en la esquina inferior izquierda de la pantalla:

![](tiled-images/image6.png "Si se ejecuta el juego, el icono de mapa aparecen en la esquina inferior izquierda de la pantalla")


## <a name="considerations-for-rendering-pixel-art"></a>Consideraciones para representar el arte de píxeles

Arte píxel, en el contexto de desarrollo de videojuegos, hace referencia al arte visual 2D que normalmente se crea mediante la mano y, a menudo es una resolución baja. Arte de píxeles puede ser modo restrictivo intensivas para crear, por lo que píxel arte icono conjuntos suelen incluyen iconos de baja resolución, como 16 o 32 ancho en píxeles y el alto de tiempo. Si no se escala en tiempo de ejecución, el arte de píxel a menudo es demasiado pequeño para tabletas y teléfonos más modernos.

Podemos ajustar las dimensiones mostradas en archivo de GameAppDelegate.cs de nuestro juego, donde agregaremos una llamada a `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Para obtener más información sobre `CCSceneResolutionPolicy`, consulte nuestra guía sobre [control resoluciones en CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Si se ejecuta el juego ahora, veremos el juego ocupan la pantalla completa de nuestro dispositivo:

![](tiled-images/image7.png "El juego ocupan la pantalla completa del dispositivo")

Por último desearemos deshabilitar el suavizado de contorno en nuestro mapa del icono. El `Antialiased` propiedad aplica un efecto de desenfoque al representar los objetos que son ampliados. Suavizado de contorno puede ser útil para reducir el aspecto pixelado de objetos gráficos, pero también puede presentar sus propios artefactos de representación. En concreto, el suavizado de contorno desenfoca el contenido de cada mosaico. Sin embargo, los bordes de cada mosaico no desenfoque, lo que hace que los iconos individuales destaquen en lugar de fusión con iconos adyacentes. También debemos mencionar que juegos de arte píxel a menudo conservan su aspecto pixelado para mantener un *retro* sentir.

Establecer `Antialiased` a `false` después de crear el `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

Ahora nuestro mapa del mosaico no aparecerá borrosa:

![](tiled-images/image8.png "Ahora el mapa de mosaico no aparecerá borroso")


## <a name="using-tile-properties-at-runtime"></a>Uso de propiedades de los iconos en tiempo de ejecución

Hasta ahora, tenemos un `CCTileMap` carga un archivo .tmx y mostrarlos, pero no tenemos que se puede interactuar con él. En concreto, determinados iconos (por ejemplo, nuestro pecho Tesoro) deben tener una lógica personalizada. Se paso a paso cómo detectar propiedades del mosaico personalizado y las diversas formas de responder a estas propiedades una vez identificadas en tiempo de ejecución.

Antes de escribir ningún código, necesitamos agregar propiedades a nuestro mapa del icono a través de mosaico. Para ello, abra el archivo de dungeon.tmx en el programa de mosaico. Asegúrese de abrir el archivo que se usa en el proyecto de juego.

Una vez abierto, seleccione el Tesoro en el icono del conjunto para ver sus propiedades:

![](tiled-images/image9.png "Una vez abierto, seleccione el Tesoro en el icono del conjunto para ver sus propiedades")

Si no aparecen las propiedades de pecho Tesoro, haga doble clic en el pecho Tesoro y seleccione **propiedades del mosaico**:

![](tiled-images/image10.png "Si no aparecen las propiedades de pecho Tesoro, haga doble clic en el pecho Tesoro y seleccione Propiedades del mosaico")

Propiedades del mosaico se implementan con un nombre y un valor. Para agregar una propiedad, haga clic en el **+** botón, escriba el nombre **IsTreasure**, haga clic en **Aceptar**, a continuación, escriba el valor **true**: 

![](tiled-images/image11.png "Para agregar una propiedad, haga clic en el botón, escriba el nombre IsTreasure, haga clic en Aceptar y luego escriba el valor true")

No se olvide de guardar el archivo .tmx después de modificar las propiedades.

Por último, vamos a agregar código para buscar la propiedad recién agregada. Se pasará por cada `CCTileMapLayer` (nuestro mapa tiene 2 niveles), a continuación, a través de cada fila y columna para buscar todos los elementos que tienen el `IsTreasure` propiedad:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

La mayoría del código no necesita explicación, pero debemos analizamos el tratamiento de los iconos del Tesoro. En este caso Estamos quitando todos los elementos que se identifican como cofres. Esto es porque cofres probablemente necesitará código personalizado en tiempo de ejecución a colisiones de efecto y a conceder al Reproductor el contenido del Tesoro cuando se abre. Además, es posible que deba el Tesoro reaccionar a los que se va a abrir (cambiar su apariencia visual) y puede tener lógica para que sólo aparezca cuando todo en pantalla enemigos han sido rechaza.

En otras palabras, el Tesoro se beneficiará de que se va a una entidad en lugar de que un icono sencillo en el `CCTileMap`. Para obtener más información sobre las entidades de juegos, consulte el [guiar las entidades de CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="summary"></a>Resumen

Este tutorial describe cómo cargar archivos .tmx creados por el mosaico en una aplicación de CocosSharp. Muestra cómo modificar la resolución de la aplicación para tener en cuenta el arte de píxel de menor resolución y cómo buscar iconos por sus propiedades para realizar lógica personalizada, como la creación de instancias de entidad.

## <a name="related-links"></a>Vínculos relacionados

- [Sitio Web en mosaico](http://www.mapeditor.org/)
- [Contenido zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
