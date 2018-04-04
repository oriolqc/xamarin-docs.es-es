---
title: CocosSharp
description: Vínculos de este documento a varios artículos sobre desarrollo de juegos con CocosSharp.
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: 2772af61dfc60b7ecd0fd5f7ecdfe5701d2504f0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="cocossharp"></a>CocosSharp

_CocosSharp es una biblioteca para la compilación de juegos 2D mediante C# y F #. Es un puerto .NET del motor Cocos2D popular._

## <a name="introduction-to-cocossharp"></a>Introducción a CocosSharp

El motor de juegos 2D CocosSharp proporciona tecnología para hacer que los juegos multiplataforma. Para obtener una lista completa de las plataformas compatibles, consulte el [CocosSharp wiki en GitHub](https://github.com/mono/CocosSharp/wiki).
Estas guías usan C# para obtener ejemplos de código, aunque también es completamente funcional con F # CocosSharp.

Proporciona el núcleo de CocosSharp el [MonoGame framework](http://www.monogame.net/), que es en sí misma un multiplataforma, acelerados por hardware API que proporciona gráficos, administración de Estados de audio, juegos, entrada y una canalización de contenido para la importación de activos.
CocosSharp es una capa de abstracción eficaz idónea para juegos 2D.
Además, juegos más grandes pueden realizar sus propias optimizaciones fuera de sus bibliotecas de core juegos aumenta la complejidad. En otras palabras, CocosSharp proporciona una combinación de facilidad de uso y rendimiento, permitiendo a los programadores a empezar a trabajar rápidamente sin perjuicio de juego tamaño o complejidad.

Esta práctica vídeo muestra cómo crear un sencillo CocosSharp multiplataforma juegos:

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

Esta guía describe BouncingGame, incluida la forma de trabajar con contenido, los diversos elementos visuales que se utilizan para crear un juego, agregar lógica de juego y mucho más.

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Juego de Fruity corresponden a las fechas](~/graphics-games/cocossharp/fruity-falls.md)

![Captura de pantalla de juego Fruity corresponden a las fechas](images/fruity-falls.png "Fruity corresponden a las fechas de pantalla de juegos")

Esta guía describe el juego Fruity corresponden a las fechas, tratan conceptos de desarrollo de juegos como física, administración de contenido, estado del juego y diseño de juego y CocosSharp comunes.  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Juego de tiempo de moneda](~/graphics-games/cocossharp/cointime.md)

![Captura de pantalla de juego de tiempo de moneda](images/cointime.png "captura de pantalla de juego de tiempo de moneda")

Hora de la moneda es un juego para iOS y Android reformador de platino completa. El objetivo del juego consiste en recopilar todas las monedas en un nivel y, a continuación, llegar a la puerta de salida evitando enemigos y obstáculos al mismo.

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[Geometría de dibujo con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md)

![Formas dibujadas con CCDrawNode](images/ccdrawnode.png "formas dibujadas con CCDrawNode")

CCDrawNode proporciona métodos para dibujar objetos simples como líneas, círculos y triángulos.

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[Animación con CCAction](~/graphics-games/cocossharp/ccaction.md)

![Una animación de CCAction](images/ccaction.png "CCAction una animación")

`CCAction` es una clase base que puede usarse para animar objetos CocosSharp. Esta guía explica integrada `CCAction` implementaciones para las tareas comunes, como la posición, la escala y la rotación. También examina cómo crear las implementaciones personalizadas heredando de `CCAction`.

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[Uso de Tiled con CocosSharp](~/graphics-games/cocossharp/tiled.md)

![Un nivel en un juego](images/tiled.png "un nivel en un juego")

Mosaico es una eficaz, flexible y su aplicación es para la creación de mosaico ortogonal e isométrica se asigna para juegos. CocosSharp proporciona integración incorporada para el formato de archivo nativo del mosaico.

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[Entidades de CocosSharp](~/graphics-games/cocossharp/entities.md)

![Una nave espacial de un juego](images/entities.png "una nave espacial de un juego")

El modelo de entidad es una manera eficaz para organizar el código de juego. Mejora la legibilidad, hace que el código sea más fácil de mantener y aprovecha la funcionalidad integrada de elemento primario/secundario.

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[Control de varias resoluciones en CocosSharp](~/graphics-games/cocossharp/resolutions.md)

![Una cuadrícula que representa la resolución de pantalla](images/resolutions.png "una cuadrícula que representa la resolución de pantalla")

Esta guía muestra cómo trabajar con CocosSharp para desarrollar los juegos que se muestren correctamente en los dispositivos de distintas resoluciones.

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[Canalización de contenido de CocosSharp](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

Contenido de las canalizaciones se suelen usar en el desarrollo de juego para optimizar el contenido y darle formato, que se puede cargar en determinado hardware o con determinados marcos de desarrollo de juegos.

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[Mejorar la velocidad de fotogramas con CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![Un árbol de un CCSpriteSheet](images/ccspritesheet.png "un árbol de un CCSpriteSheet")

`CCSpriteSheet` Proporciona funcionalidad para combinar y usar muchos archivos de imagen en una textura. Reducir el recuento de textura puede mejorar los tiempos de carga de un juego y velocidad de fotogramas.

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[Textura, almacenamiento en caché mediante CCTextureCache](~/graphics-games/cocossharp/texture-cache.md)

![Una representación de cómo CocosSharp almacena en memoria caché de imágenes](images/texture-cache.png "una representación de cómo CocosSharp almacena en memoria caché de imágenes")

Del CocosSharp `CCTextureCache` clase proporciona un método estándar para organizar, almacenar en caché y descargar contenido. 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[Operaciones matemáticas 2D con CocosSharp](~/graphics-games/cocossharp/math.md)

![Una imagen que se va a girar](images/math.png "una imagen que se va a girar")

Esta guía incluye matemáticas 2D para desarrollo de juegos. Usa CocosSharp para mostrar cómo realizar tareas comunes de desarrollo de juegos y explica las matemáticas que hay detrás de estas tareas.

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[Rendimiento y efectos visuales con CCRenderTexture](~/graphics-games/cocossharp/ccrendertexture.md)

![Un objeto sprite de un juego](images/ccrendertexture.png "un sprite de un juego")

La `CCRenderTexture` clase proporciona funcionalidad para representar varios objetos de CocosSharp a una textura única. Una vez creada, `CCRenderTexture` instancias pueden usarse para representar gráficos de forma eficaz y para implementar los efectos visuales.
