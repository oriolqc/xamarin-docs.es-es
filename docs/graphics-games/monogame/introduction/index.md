---
title: Introducción al desarrollo de juegos con MonoGame
description: En este tutorial de varias partes, se muestra cómo crear una sencilla aplicación de 2D con MonoGame.  Abarca juego comunes conceptos de programación, como los gráficos, la entrada, juegos entidades y física.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 46cc3a7e3bb6c58e04626c9d2cc9437c16ba19f5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="introduction-to-game-development-with-monogame"></a>Introducción al desarrollo de juegos con MonoGame

_En este tutorial de varias partes, se muestra cómo crear una sencilla aplicación de 2D con MonoGame.  Abarca juego comunes conceptos de programación, como los gráficos, la entrada, juegos entidades y física._

Este artículo describe la tecnología MonoGame API para hacer que los juegos multiplataforma. Para obtener una lista completa de plataformas, consulte la [sitio Web de MonoGame](http://www.monogame.net/). Este tutorial usará C# para obtener ejemplos de código, aunque también es completamente funcional con F # MonoGame.

MonoGame es una plataforma cruzada, acelerados por hardware API que proporciona gráficos, administración de Estados de audio, juegos, entrada y una canalización de contenido para la importación de activos. A diferencia de la mayoría de los motores de juego, MonoGame no proporcionan ni impone ninguna estructura de modelo o un proyecto.  Aunque esto significa que los desarrolladores son gratuitas organizar el código que deseen, también significa que se necesita un poco de código de programa de instalación cuando primero inicie un nuevo proyecto.

La primera sección de este tutorial se centra en la configuración de un proyecto vacío. La última sección cubre escribiendo toda nuestra lógica de juego y el contenido, la mayoría de los cuales será todas las plataformas.

Al final de este tutorial, se habrá creado un juego sencillo que el Reproductor puede controlar un objeto animado con entrada táctil.  Aunque esto no es técnicamente un completo juego (ya que éste no tiene ningún ganar o perder condiciones), muestra los conceptos de desarrollo de juegos en numerosas y puede utilizarse como base para muchos tipos de juegos. 

A continuación muestra el resultado de este tutorial:

![Animación de caracteres de juegos de ejemplo siguiendo el mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame y XNA

La biblioteca de MonoGame está diseñada para imitar la biblioteca de Microsoft XNA de sintaxis y funcionalidad.  Todos los objetos de MonoGame existen en el espacio de nombres Microsoft.Xna: permitir que la mayoría del código XNA para usarse en MonoGame sin modificaciones. 

Los programadores familiarizados con XNA ya estará familiarizados con la sintaxis del MonoGame y los desarrolladores que buscan información adicional sobre cómo trabajar con MonoGame podrán hacer referencia a tutoriales XNA en línea existente, documentación de la API y las discusiones.


## <a name="walkthrough-parts"></a>Partes de tutorial

- [Parte 1: crear un proyecto de MonoGame multiplataforma](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2: implementar la WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Vínculos relacionados

- [Proyecto de MonoGame WalkingGame (ejemplo)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [Fuentes XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB fuentes Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android en NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame en NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentación de API de MonoGame](http://www.monogame.net/documentation/?page=main)
