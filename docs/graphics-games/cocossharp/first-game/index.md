---
title: Introducción al desarrollo de juegos con CocosSharp
description: En este tutorial de varias partes, se muestra cómo crear un juego 2D simple usando CocosSharp. Abarca conceptos de programación juegos comunes, como gráficos, las entradas y física.
ms.topic: article
ms.prod: xamarin
ms.assetid: BCA99A61-A48D-4207-9A35-4C62F10C9AA5
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 5ab6f68aed791dd21516d663367ac5435e92d6cc
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="introduction-to-game-development-with-cocossharp"></a>Introducción al desarrollo de juegos con CocosSharp

_En este tutorial de varias partes, se muestra cómo crear un juego 2D simple usando CocosSharp. Abarca conceptos de programación juegos comunes, como gráficos, las entradas y física._

El motor de juegos 2D CocosSharp proporciona tecnología para hacer que los juegos multiplataforma. Para obtener una lista completa de las plataformas compatibles, consulte el [CocosSharp wiki en GitHub](https://github.com/mono/CocosSharp/wiki). Este tutorial usará C# para obtener ejemplos de código, aunque también es completamente funcional con F # CocosSharp.

Proporciona el núcleo de CocosSharp el [MonoGame framework](http://www.monogame.net/), que es en sí misma un multiplataforma, acelerados por hardware API que proporciona gráficos, administración de Estados de audio, juegos, entrada y una canalización de contenido para la importación de activos. CocosSharp es una capa de abstracción eficaz idónea para juegos 2D. Además, juegos más grandes pueden realizar sus propias optimizaciones fuera de sus bibliotecas de core juegos aumenta la complejidad. En otras palabras, CocosSharp proporciona una combinación de facilidad de uso y rendimiento, permitiendo a los programadores a empezar a trabajar rápidamente sin perjuicio de juego tamaño o complejidad.

La primera sección de este tutorial se centran en la configuración de un proyecto vacío.  La segunda parte trata escribiendo toda la lógica de juego. 

Al final de este tutorial se habrá creado un simple juego donde es el objetivo del Reproductor a la diapositiva una paleta horizontalmente para intentar mantener una bola caiga fuera de la pantalla. Cada rebote aumentará la puntuación del Reproductor, un punto.

![](images/image1.png "Cada rebote aumentará la puntuación del Reproductor, un punto")

# <a name="walkthrough-parts"></a>Partes de tutorial

* [Parte 1: crear un proyecto CocosSharp](~/graphics-games/cocossharp/first-game/part1.md)
* [Parte 2: implementar la BouncingGame](~/graphics-games/cocossharp/first-game/part2.md)

## <a name="related-links"></a>Vínculos relacionados

- [Contenido de juego (ejemplo)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
- [Proyecto completado (ejemplo)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [PCL CocosSharp en NuGet](http://www.nuget.org/packages/CocosSharp.PCL.Shared/)
- [Documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
