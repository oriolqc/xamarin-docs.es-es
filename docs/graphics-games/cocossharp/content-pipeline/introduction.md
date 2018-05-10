---
title: Introducción a las canalizaciones de contenido
description: Las canalizaciones son aplicaciones o partes de las aplicaciones de contenido, que se usan para convertir archivos en un formato que se pueden cargar proyectos de juego. La canalización de contenido de MonoGame es una implementación de la canalización de contenido específica para la conversión de archivos para los proyectos CocosSharp y MonoGame.
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: a369c5ba61033eb61c0f188c03b21e08c71784fb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="introduction-to-content-pipelines"></a>Introducción a las canalizaciones de contenido

_Las canalizaciones son aplicaciones o partes de las aplicaciones de contenido, que se usan para convertir archivos en un formato que se pueden cargar proyectos de juego. La canalización de contenido de MonoGame es una implementación de la canalización de contenido específica para la conversión de archivos para los proyectos CocosSharp y MonoGame._

Este artículo proporciona una explicación de los conceptos de las canalizaciones de contenido, se centra principalmente en la *MonoGame Content Pipeline*, que es una implementación de contenido de canalización usada con CocosSharp y MonoGame.


## <a name="what-is-a-content-pipeline"></a>¿Qué es una canalización de contenido?

El término *canalización contenido* es un término general para el proceso de convertir un archivo de un formato a otro. El *entrada* de la canalización de contenido suele ser un archivo que se produce por una herramienta de creación, como archivos de imagen de Photoshop. La canalización de contenido crea el *salida* archivo en un formato que se puede cargar directamente mediante un proyecto de juego. Normalmente, los archivos de salida están optimizados para la carga rápida y reducción el tamaño del disco.

Contenido de canalizaciones pueden ser de línea de comandos ejecutables, aplicaciones basadas en GUI dedicadas o complementos incrustan en otra aplicación, como Visual Studio. Canalizaciones contenidas normalmente se ejecutan antes de que se ejecuta el juego. Si la canalización de contenido está asociada a alguna aplicación como Visual Studio, puede ejecutar durante el tiempo de compilación. Si la canalización de contenido es una aplicación independiente, puede ejecutar al indicar explícitamente que lo haga por el usuario. La aplicación o lógica responsable de convertir un archivo de entrada específico (como un **.png**) a una salida asociada archivo se denomina un *generador*. 

Podemos visualizar la ruta de acceso que toma un archivo de creación que se va a cargar en tiempo de ejecución como se indica a continuación:

![](introduction-images/image1.png "La ruta de acceso que toma un archivo de creación que se va a cargar en tiempo de ejecución se visualiza en este diagrama")

## <a name="why-use-a-content-pipeline"></a>¿Por qué usar una canalización de contenido?

Canalizaciones contenidas introducen un paso adicional entre la aplicación de creación y el juego, lo que puede aumentar los tiempos de compilación y agregar complejidad al proceso de desarrollo. A pesar de estas consideraciones, canalizaciones contenidas presentan a una serie de beneficios para el desarrollo de juegos:


### <a name="converting-to-a-format-understood-by-the-game"></a>Convertir a un formato entiende el juego

CocosSharp y MonoGame proporcionan métodos para cargar varios tipos de contenido; Sin embargo, el contenido debe tener el formato correctamente antes de que se va a cargar. Mayoría de los tipos de contenido requiere algún tipo de conversión antes de que se va a cargar. Por ejemplo, los efectos de sonido en el **.wav** formato se debe convertir en un **.xnb** archivo que se cargará en tiempo de ejecución porque CocosSharp y MonoGame no admite la carga del **.wav** formato de archivo.


### <a name="converting-to-a-format-native-to-the-hardware"></a>Convertir a un formato nativo para el hardware

Otro hardware puede tratar contenido de manera diferente en tiempo de ejecución. Por ejemplo, juegos de CocosSharp pueden cargar archivos de imagen al crear un `CCSprite` instancia. Aunque el mismo código se puede utilizar para cargar los archivos en iOS y Android, cada plataforma almacena el archivo cargado de forma diferente. En consecuencia, la canalización de contenido MonoGame formatos de textura **.xnb** archivos de forma diferente dependiendo de la plataforma de destino.


### <a name="reducing-size-on-disk"></a>Reducir el tamaño en disco 

Las canalizaciones se pueden utilizar para quitar información de contenido, que es útil en tiempo de autor pero no es necesario en tiempo de ejecución. El archivo original (entrado) puede almacenar toda la información que puede ayudar a los creadores de contenido a mantener el contenido existente, pero el archivo de salida puede ser reducido para mantener el archivo general juego pequeño. Esta consideración es especialmente útil para los juegos móviles que se descargan en lugar de distribuidas en medio de instalación.


### <a name="reducing-load-time"></a>Reduciendo el tiempo de carga

Juegos pueden requerir modificaciones del contenido para mejorar el rendimiento en tiempo de ejecución, para mejorar objetos visuales, o para agregar nuevas características. Por ejemplo, muchos juegos 3D calcular iluminación una vez, entonces utilizar el resultado de este cálculo al representar escenas complejas. Desde la realización de estos cálculos cuando cargue contenido puede ser prohibitivo el cálculo en su lugar puede realizarse cuando se compila el juego. Los cálculos resultantes pueden incluirse en el contenido, lo que permite el contenido que se va a cargar con mayor rapidez que sería posible. 


## <a name="xnb-file-extension"></a>extensión de archivo xnb

El **.xnb** extensión de archivo es la extensión para todos los archivos que se va a producir la canalización de contenido de Monogame. Esto coincide con la extensión de los archivos que genera la canalización de contenido de Microsoft XNA.

El **.xnb** extensión se usa independientemente del tipo de archivo original. En otras palabras, los archivos de imagen (**.png**), archivos de audio (**.wav**), y los tipos de archivo personalizados se se emite como **.xnb** archivos cuando se pasan a través de la canalización de contenido. Puesto que la extensión no se puede usar para distinguir entre diferentes formatos de archivo, a continuación, CocosSharp y MonoGame métodos que cargan **.xnb** archivos no esperan las extensiones al cargar el archivo.

Archivos de .xnb CocosSharp y MonoGame pueden crearse con la herramienta de canalización Monogame que se trata [en este tutorial](~/graphics-games/cocossharp/content-pipeline/walkthrough.md).


## <a name="summary"></a>Resumen

Este artículo proporciona información general y las ventajas de las canalizaciones de contenido por lo general, así como una introducción a la canalización de contenido MonoGame.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de canalización MonoGame](http://www.monogame.net/documentation/?page=Pipeline)
