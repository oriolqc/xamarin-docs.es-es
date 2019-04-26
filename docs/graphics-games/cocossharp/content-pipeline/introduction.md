---
title: Introducción a las canalizaciones de contenido
description: Las canalizaciones son aplicaciones o partes de las aplicaciones de contenido, que se usan para convertir archivos en un formato que se puede cargar los proyectos de juegos. La canalización de contenido MonoGame es una implementación de la canalización de contenido específicos para convertir los archivos de proyectos de CocosSharp y MonoGame.
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 712c430fb6309ba0f5c3e573267c59e422de8ad2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029531"
---
# <a name="introduction-to-content-pipelines"></a>Introducción a las canalizaciones de contenido

_Las canalizaciones son aplicaciones o partes de las aplicaciones de contenido, que se usan para convertir archivos en un formato que se puede cargar los proyectos de juegos. La canalización de contenido MonoGame es una implementación de la canalización de contenido específicos para convertir los archivos de proyectos de CocosSharp y MonoGame._

Este artículo proporciona una explicación de las canalizaciones de contenido, centrándose principalmente en los conceptos del *MonoGame Content Pipeline*, que es una implementación de canalización de contenido que se usa con CocosSharp y MonoGame.


## <a name="what-is-a-content-pipeline"></a>¿Qué es una canalización de contenido?

El término *canalización de contenido* es un término general para el proceso de convertir un archivo de un formato a otro. El *entrada* de la canalización de contenido suele ser un archivo que se va a producir una herramienta de creación, como archivos de imagen de Photoshop. La canalización de contenido se crea el *salida* archivo en un formato que se puede cargar directamente mediante un proyecto de juego. Normalmente, los archivos de salida están optimizados para la carga rápida y reducción el tamaño del disco.

Contenido de las canalizaciones pueden ser de línea de comandos ejecutables, aplicaciones basadas en GUI dedicadas o complementos incrustan en otra aplicación como Visual Studio. Las canalizaciones de contenido normalmente se ejecutan antes de que se ejecuta el juego. Si la canalización de contenido está asociada a alguna aplicación, como Visual Studio, puede ejecutar durante el tiempo de compilación. Si la canalización de contenido es una aplicación independiente, puede ejecutar cuando se le indicase explícitamente hacerlo por el usuario. La aplicación o la lógica responsable de convertir un archivo de entrada concreto (como un **.png**) a una salida asociada archivo se denomina un *generador*. 

Podemos visualizar la ruta de acceso que toma un archivo desde la creación hasta que se va a cargar en tiempo de ejecución como sigue:

![](introduction-images/image1.png "La ruta de acceso que toma un archivo desde la creación hasta que se va a cargar en tiempo de ejecución se visualiza en este diagrama")

## <a name="why-use-a-content-pipeline"></a>¿Por qué usar una canalización de contenido?

Las canalizaciones de contenido presentan un paso adicional entre la aplicación de creación y el juego, que puede aumentar los tiempos de compilación y agregar complejidad al proceso de desarrollo. A pesar de estas consideraciones, las canalizaciones de contenido presentan a una serie de ventajas al desarrollo de juegos:


### <a name="converting-to-a-format-understood-by-the-game"></a>Convertir en un formato entendido el juego

CocosSharp y MonoGame proporcionan métodos para cargar varios tipos de contenido. Sin embargo, el contenido debe tener el formato correctamente antes de que se está cargando. Mayoría de los tipos de contenido requiere algún tipo de conversión antes de que se está cargando. Por ejemplo, los efectos de sonido en el **.wav** formato se debe convertir en un **.xnb** archivo que se cargará en tiempo de ejecución, ya que no admiten la carga CocosSharp y MonoGame el **.wav** formato de archivo.


### <a name="converting-to-a-format-native-to-the-hardware"></a>Convertir en un formato nativo para el hardware

Un hardware diferente puede tratar el contenido diferente en tiempo de ejecución. Por ejemplo, juegos de CocosSharp pueden cargar archivos de imagen al crear un `CCSprite` instancia. Aunque puede utilizarse el mismo código para cargar los archivos en iOS y Android, cada plataforma almacena el archivo cargado de forma diferente. Como consecuencia, la canalización de contenido MonoGame formatos de textura **.xnb** archivos de forma distinta dependiendo de la plataforma de destino.


### <a name="reducing-size-on-disk"></a>Disminuye el tamaño en disco 

Canalizaciones pueden usarse para quitar la información de contenido, que es útil en tiempo de autor, pero no es necesario en tiempo de ejecución. El archivo original (entrado) puede almacenar toda la información que puede ayudar a los creadores de contenido para mantener el contenido existente, pero el archivo de salida puede ser reducido para mantener reducido el archivo del juego en general. Esta consideración es especialmente útil para juegos móviles que se descargan en lugar de distribuidas en medio de instalación.


### <a name="reducing-load-time"></a>Reduciendo el tiempo de carga

Juegos pueden requerir modificaciones de contenido para mejorar el rendimiento en tiempo de ejecución, para mejorar los efectos visuales, o para agregar nuevas características. Por ejemplo, muchos juegos 3D calcular la iluminación una vez y luego usan el resultado de este cálculo al representar escenas complejas. Desde estos cálculos cuando cargue contenido puede ser prohibitivo en su lugar, se puede realizar el cálculo cuando se compila el juego. Los cálculos resultantes pueden incluirse en el contenido, lo que permite el contenido en cargarse mucho más rápidamente de lo contrario, sería posible. 


## <a name="xnb-file-extension"></a>extensión de archivo xnb

El **.xnb** es la extensión de archivo para todos los archivos generados por la canalización de contenido de Monogame. Esto coincide con la extensión de los archivos generados por la canalización de contenido de Microsoft XNA.

El **.xnb** extensión se usa independientemente del tipo de archivo original. En otras palabras, los archivos de imagen (**.png**), archivos de audio (**.wav**), y los tipos de archivo personalizados se cifrará como **.xnb** archivos cuando se pasa a través de la canalización de contenido. Puesto que la extensión no se puede usar para distinguir entre diferentes formatos de archivo, a continuación, CocosSharp y MonoGame métodos que cargan **.xnb** archivos no esperan que las extensiones al cargar el archivo.

Se pueden crear archivos de .xnb CocosSharp y MonoGame utilizando la herramienta de canalización Monogame que está cubierta [en este tutorial](~/graphics-games/cocossharp/content-pipeline/walkthrough.md).


## <a name="summary"></a>Resumen

En este artículo proporciona información general y las ventajas de las canalizaciones de contenido en general, así como una introducción a la canalización de contenido de MonoGame.

## <a name="related-links"></a>Vínculos relacionados

- [Documentación de la canalización de MonoGame](http://www.monogame.net/documentation/?page=Pipeline)
