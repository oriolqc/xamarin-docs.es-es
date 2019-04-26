---
title: Introducción al desarrollo de juegos con MonoGame
description: Este tutorial de varias partes muestra cómo crear una sencilla aplicación de 2D con MonoGame.  Se describe cómo common juego conceptos de programación, como gráficos, de entrada, de juegos entidades y física.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4ab98d59bc74672f9531f4dbd3c33a6270582612
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386281"
---
# <a name="introduction-to-game-development-with-monogame"></a>Introducción al desarrollo de juegos con MonoGame

_Este tutorial de varias partes muestra cómo crear una sencilla aplicación de 2D con MonoGame.  Se describe cómo common juego conceptos de programación, como gráficos, de entrada, de juegos entidades y física._

Este artículo describe la tecnología para crear juegos multiplataforma MonoGame API. Para obtener una lista completa de las plataformas, vea el [sitio Web de MonoGame](http://www.monogame.net/). Este tutorial se utilizará C# para obtener ejemplos de código, aunque es completamente funcional con MonoGame F# también.

MonoGame es multiplataforma, proporcionar gráficos, administración de Estados de juegos, audio, entrada y una canalización de contenido para la importación de los recursos de API de aceleración de hardware. A diferencia de la mayoría de los motores de juegos, MonoGame no proporcionan ni impone ninguna estructura de modelo o el proyecto.  Aunque esto significa que los desarrolladores son gratuitos organizar su código como deseen, también significa que se necesita un poco de código de programa de instalación cuando se inicia en primer lugar un nuevo proyecto.

La primera sección de este tutorial se centra en la configuración de un proyecto vacío. La última sección trata de escribir todos nuestros lógica del juego y el contenido, la mayoría de los cuales será multiplataforma.

Al final de este tutorial, se habrá creado un juego sencillo que el Reproductor puede controlar un objeto animado con entrada táctil.  Aunque esto no es técnicamente un juego completo (ya que no tiene ningún ganará o perderá condiciones), muestra los conceptos de desarrollo de juegos numerosas y puede usarse como base para muchos tipos de juegos. 

A continuación muestra el resultado de este tutorial:

![Animación de personajes del juego de ejemplo siguiendo el mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame y XNA

La biblioteca de MonoGame está pensada para imitar la biblioteca de Microsoft XNA en la sintaxis y la funcionalidad.  Todos los objetos de MonoGame existen en el espacio de nombres Microsoft.Xna: lo que permite la mayoría del código XNA para usarse en MonoGame sin ninguna modificación. 

Los desarrolladores familiarizados con XNA ya estará familiarizados con la sintaxis de MonoGame y los desarrolladores que desean para obtener más información sobre cómo trabajar con MonoGame podrán hacer referencia a tutoriales XNA en línea existente, la documentación de API y discusiones.


## <a name="walkthrough-parts"></a>Partes del tutorial

- [Parte 1: crear un proyecto de MonoGame multiplataforma](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2: implementación de WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Vínculos relacionados

- [Proyecto de WalkingGame MonoGame (ejemplo)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [Fuentes XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB fuentes Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android en NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame en NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentación de API MonoGame](http://www.monogame.net/documentation/?page=main)
