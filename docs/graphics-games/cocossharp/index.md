---
title: Motor de juego 2D CocosSharp
description: Este documento incluye vínculos a varios artículos sobre desarrollo de juegos con CocosSharp. Contenido vinculado describe las aplicaciones de ejemplo, dibujo, animación y mucho más.
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: add73360ea98d8c516e413f0cc0264f68c58d79d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107019"
---
# <a name="cocossharp-2d-game-engine"></a>Motor de juego 2D CocosSharp

_CocosSharp es una biblioteca para crear juegos en 2D mediante C# y F#. Es un puerto .NET del motor Cocos2D popular._

## <a name="introduction-to-cocossharp"></a>Introducción a CocosSharp

El motor de juegos 2D de CocosSharp proporciona tecnología para crear juegos multiplataforma. Para obtener una lista completa de las plataformas compatibles, consulte el [CocosSharp wiki de GitHub](https://github.com/mono/CocosSharp/wiki).
Use estas guías C# para obtener ejemplos de código, aunque es completamente funcional con CocosSharp F# también.

El núcleo de CocosSharp proporcionado por el [MonoGame framework](http://www.monogame.net/), que es en sí mismo una multiplataforma, proporcionar gráficos, administración de Estados de juegos, audio, entrada y una canalización de contenido para la importación de los recursos de API de aceleración de hardware.
CocosSharp es una capa de abstracción eficaz idónea para juegos en 2D.
Además, juegos de mayor tamaño pueden realizar sus propias optimizaciones fuera de sus bibliotecas de core a medida que crece juegos en complejidad. En otras palabras, CocosSharp proporciona una combinación de facilidad de uso y rendimiento, permitiendo a los desarrolladores empezar a trabajar rápidamente sin limitar el tamaño del juego o complejidad.

Este práctico vídeo muestra cómo crear un CocosSharp multiplataforma simple juego:

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

Esta guía describe BouncingGame, incluida la forma de trabajar con contenido de juegos, los diversos elementos visuales que se usa para crear un juego, agregar lógica de juego y mucho más.

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Juego Fruity corresponden a las fechas](~/graphics-games/cocossharp/fruity-falls.md)

![Juego Fruity captura de pantalla](images/fruity-falls.png "del juego Fruity captura de pantalla")

Esta guía describe el juego Fruity caídas, que abarcan CocosSharp comunes y conceptos de desarrollo de juegos, como diseño de juegos, administración de contenido, estado del juego y física.  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Juego de tiempo de moneda](~/graphics-games/cocossharp/cointime.md)

![Coin captura de pantalla de juego de tiempo](images/cointime.png "captura de pantalla de juego de tiempo de moneda")

Tiempo de moneda es una completa plataforma de juego para iOS y Android. El objetivo del juego consiste en recopilar todos de monedas en un nivel y, a continuación, llegar a la puerta de salida al tiempo que evita los enemigos y obstáculos.

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[Dibujar geometría con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md)

![Formas dibujadas con CCDrawNode](images/ccdrawnode.png "formas dibujadas con CCDrawNode")

CCDrawNode proporciona métodos para dibujar objetos simples como líneas, círculos y triángulos.

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[Animación con CCAction](~/graphics-games/cocossharp/ccaction.md)

![Una animación con CCAction](images/ccaction.png "CCAction una animación")

`CCAction` es una clase base que se puede usar para animar objetos CocosSharp. Esta guía explica integrado `CCAction` implementaciones para tareas comunes, como la posición, la escala y rotación. También examina cómo crear implementaciones personalizadas heredando de `CCAction`.

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[Uso de Tiled con CocosSharp](~/graphics-games/cocossharp/tiled.md)

![Un nivel en un juego](images/tiled.png "un nivel en un juego")

Mosaico es una solución eficaz, flexible y asigna su aplicación es para la creación de icono ortogonal e isométrica para juegos. CocosSharp proporciona integración incorporada para formato de archivo nativo del mosaico.

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[Entidades de CocosSharp](~/graphics-games/cocossharp/entities.md)

![Una nave espacial de un juego](images/entities.png "una nave espacial de un juego")

El modelo de entidad es una manera eficaz para organizar el código de juego. Mejora la legibilidad, hace que el código sea más fácil de mantener y aprovecha la funcionalidad integrada de elementos primarios/secundarios.

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[Control de varias resoluciones en CocosSharp](~/graphics-games/cocossharp/resolutions.md)

![Una cuadrícula que representa la resolución de pantalla](images/resolutions.png "una cuadrícula que representa la resolución de pantalla")

Esta guía muestra cómo trabajar con CocosSharp para desarrollar juegos que se muestren correctamente en los dispositivos de distintas resoluciones.

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[Canalización de contenido de CocosSharp](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

Las canalizaciones de contenido a menudo se usan en el desarrollo de juegos para optimizar el contenido y darle formato, que se puede cargar en determinado hardware o con determinados marcos de desarrollo de juegos.

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[Mejorar la velocidad de fotogramas con CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![Un árbol de un CCSpriteSheet](images/ccspritesheet.png "un árbol desde un CCSpriteSheet")

`CCSpriteSheet` Proporciona funcionalidad para combinar y el uso de muchos archivos de imagen en una textura. Reducir recuento de textura puede mejorar los tiempos de carga de un juego y velocidad de fotogramas.

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[Textura de almacenamiento en caché mediante CCTextureCache](~/graphics-games/cocossharp/texture-cache.md)

![Una representación de cómo CocosSharp almacena en memoria caché de imágenes](images/texture-cache.png "una representación de cómo CocosSharp almacena en memoria caché de imágenes")

De CocosSharp `CCTextureCache` clase proporciona una manera estándar para organizar y almacenar en caché para descargar contenido. 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[Matemáticas 2D con CocosSharp](~/graphics-games/cocossharp/math.md)

![Una imagen que se giran](images/math.png "una imagen que se va a girar")

Esta guía trata las matemáticas 2D para el desarrollo de juegos. Usa CocosSharp para mostrar cómo realizar tareas comunes de desarrollo de juegos y explica las matemáticas detrás de estas tareas.

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[Rendimiento y efectos visuales con CCRenderTexture](~/graphics-games/cocossharp/ccrendertexture.md)

![Un sprite desde un juego](images/ccrendertexture.png "un sprite desde un juego")

La `CCRenderTexture` clase proporciona funcionalidad para representar varios objetos de CocosSharp a una textura única. Una vez creada, `CCRenderTexture` instancias pueden usarse para representar gráficos de forma eficaz y para implementar los efectos visuales.
