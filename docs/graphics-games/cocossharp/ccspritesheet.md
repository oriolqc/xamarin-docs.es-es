---
title: Mejorar la velocidad de fotogramas con CCSpriteSheet
description: CCSpriteSheet proporciona funcionalidad para combinar y utilizar muchos archivos de imagen en una textura. Reducir el recuento de textura puede mejorar los tiempos de carga de un juego y velocidad de fotogramas.
ms.topic: article
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 7e2bb5b98b5c93fb625ce645692d8a3ccb3d143b
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>Mejorar la velocidad de fotogramas con CCSpriteSheet

_CCSpriteSheet proporciona funcionalidad para combinar y utilizar muchos archivos de imagen en una textura. Reducir el recuento de textura puede mejorar los tiempos de carga de un juego y velocidad de fotogramas._

Muchos juegos exigen esfuerzos de optimización para ejecutar sin problemas y cargar rápidamente en hardware de dispositivos móvil. La `CCSpriteSheet` clase puede ayudar a resolver muchos problemas de rendimiento comunes detectados por CocosSharp juegos. Esta guía trata problemas comunes de rendimiento y cómo se deben tratar mediante la `CCSpriteSheet` clase.


## <a name="what-is-a-sprite-sheet"></a>¿Qué es una hoja de sprite?

A *hoja sprite*, lo que puede también se conoce como un *atlas de textura*, se muestra una imagen que combina varias imágenes en un archivo. Esto puede mejorar el rendimiento en tiempo de ejecución, así como los tiempos de carga de contenido.

Por ejemplo, la siguiente imagen es una hoja de sprite simple creada por tres imágenes independientes. Las imágenes individuales pueden tener cualquier tamaño y la hoja de sprite resultante no es necesaria para completarse en su totalidad:

![](ccspritesheet-images/image1.png "Las imágenes individuales pueden tener cualquier tamaño y la hoja de sprite resultante no es necesaria para completarse en su totalidad")


### <a name="render-states"></a>Estados de representación

Objetos visuales de CocosSharp (como `CCSprite`) simplificar código de representación a través de código de representación API gráfica tradicional, como MonoGame u OpenGL, que requieren la creación de los búferes de vértices (como se describe en el [dibujar gráficos 3D con Vértices en MonoGame](~/graphics-games/monogame/3d/part2.md) guía). A pesar de su sencillez, CocosSharp no elimina el costo de configuración *Estados de representación*, que son el número de veces que debe cambiar el código de representación texturas u otros Estados relacionados con la representación.

El código interno del CocosSharp representa cada elemento visual secuencialmente, recorrer el árbol visual que comienza con el actual `CCScene`. Por ejemplo, considere la posibilidad de la escena siguiente:

![](ccspritesheet-images/image2.png "Tenga en cuenta esta escena")

CocosSharp podría invalidar las cuatro estrellas en secuencia:

![](ccspritesheet-images/image3.png "CocosSharp podría invalidar las cuatro estrellas en secuencia")

Puesto que cada `CCSprite` utiliza la misma textura, CocosSharp puede agrupar todos los cuatro estrellas. Este código requiere un único representar estado (asignación de la textura estrella) por marco. Este escenario es muy eficaz.

Por supuesto, juegos muy pocas utilizan solamente una imagen. La escena siguiente presenta un gráfico de planeta:

![](ccspritesheet-images/image4.png "Esta escena presenta un gráfico de planeta")

Lo ideal es que CocosSharp debe dibujar todos los objetos Sprite con una imagen en primer lugar (por ejemplo, las estrellas) y, a continuación, el resto de los sprites con otra imagen (el planeta):

![](ccspritesheet-images/image5.png "Lo ideal es que CocosSharp debe dibujar todos los sprites usando una imagen en primer lugar, como las estrellas, a continuación, el resto de los sprites con otra imagen del planeta")

La ordenación posterior requiere dos Estados de representación: uno en la primera de ellas estrella, en el primer planeta.

Si todos los `CCSprite` instancias son elementos secundarios del mismo `CCNode`, a continuación, CocosSharp optimiza el orden de dibujo para reducir los cambios de estado de representación. Si es, por otro lado, el `CCSprite` instancias se organizan de forma que CocosSharp es no se puede optimizar el procesamiento (por ejemplo, si forman parte de distintas entidades `CCNode` instancias), a continuación, el orden no sea óptimo. A continuación muestra el orden de dibujo posibles peor, resultantes de los cinco estados de representación:

![](ccspritesheet-images/image6.png "Esto muestra el orden de dibujo posibles peor, resultantes de los cinco estados de representación")

Estados de representación pueden resultar difíciles de optimizar porque el orden de dibujo debe ser el árbol visual de `CCNode` instancias. Este árbol a menudo se estructura para que sea fácil trabajar con (por ejemplo, las entidades que contiene a sus elementos secundarios visuales) u organizan debido a la distribución deseada visual tal como se define por un intérprete.

Por supuesto, la situación ideal es tener un estado de representación única, a pesar de tener varias imágenes. Juegos de CocosSharp pueden hacerlo mediante la combinación de todas las imágenes en un único archivo, a continuación, cargar archivos (junto con sus que acompaña a **.plist** archivo) en un `CCSpriteSheet`. Mediante la `CCSpriteSheet` clase es incluso más importante para juegos que tiene un gran número de imágenes, o que tienen muy complicado diseños. 

### <a name="load-times"></a>Tiempos de carga

También se combina varias imágenes en un archivo mejora los tiempos de carga de un juego por una serie de motivos:

 - Combinar varias imágenes en un único archivo puede reducir el número total de píxeles que se usan a través de empaquetado eficaz
 - La carga de archivos menos significa menos sobrecarga por cada archivo, como analizar los encabezados de PNG
 - La carga de archivos menos requiere menos tiempo, lo que es importante para los medios basados en disco como DVDs y unidades de disco duro del equipo tradicional de búsqueda

## <a name="using-ccspritesheet-in-code"></a>Usar CCSpriteSheet en el código

Para crear un `CCSpriteSheet` instancia, el código debe proporcionar una imagen y un archivo que define las regiones de la imagen que se utilizará para cada fotograma. La imagen se puede cargar como un **.png** o **.xnb** archivo (si usa el [canalización contenido](~/graphics-games/cocossharp/content-pipeline/index.md)). El archivo de definición de los fotogramas es un **.plist** archivo que se puede crear manualmente o *TexturePacker* (que se tratará más adelante).

La aplicación de ejemplo que [puede descargarse aquí](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/), crea la `CCSpriteSheet` desde una **.png** y **.plist** archivo utilizando el código siguiente:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

Una vez cargados, el `CCSpriteSheet` contiene un `List` de `CCSpriteFrame` instancias: cada instancia que corresponde a una de las imágenes de origen utilizadas para crear la hoja completa. En el caso de los **SpriteSheetDemo** proyecto, el `CCSpriteSheet` contiene tres imágenes. El **.plist** puede inspeccionar el archivo en Visual Studio para Mac o en cualquier editor de texto para ver las imágenes que están disponibles. Si consideramos la **.plist** archivo en un editor de texto, podemos ver los tres marcos (secciones omite a hacer hincapié en los nombres de claves):


```csharp
...
<dict>
    <key>frames</key>
    <dict>
        <key>farBackground.png</key>
        ...
        <key>foreground.png</key>
        ...
<key>forestBackground.png</key>
...
```

Podemos usar la `Find` método para buscar los fotogramas por su nombre, como se indica a continuación (se omite el código para destacar `CCSpriteFrame` uso):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

Puesto que la `CCSprite` constructor puede tomar un `CCSpriteFrame` parámetro, el código nunca debe investigar los detalles de la `CCSpriteFrame`, como qué textura usa o la región de la imagen en la hoja de sprite maestro.


## <a name="creating-a-sprite-sheet-plist"></a>Crear un .plist de hoja sprite

El archivo .plist es un archivo basado en xml, que se pueden crear y editar manualmente. De forma similar, los programas de edición de imágenes pueden utilizarse para combinar varios archivos en un archivo de mayor tamaño. Desde la creación y el mantenimiento de las hojas de sprite pueden ser muy lentos, veremos el programa TexturePacker que puede exportar los archivos en el formato CocosSharp. TexturePacker ofrece una segunda y una versión "Pro" y está disponible para Windows y Mac OS. El resto de esta guía se pueden aplicar mediante el uso de la versión gratuita. 

Puede ser TexturePacker [descargado desde el sitio Web de TexturePacker](https://www.codeandweb.com/texturepacker). Cuando se abre, TexturePacker no tiene un proyecto cargado. La pantalla inicial permite agregar sprites, abrir proyectos recientes (si se han creado otros proyectos) y seleccione el formato que se utilizará para la hoja de sprite. CocosSharp utiliza el formato de datos de Cocos2D:

![](ccspritesheet-images/image7.png "CocosSharp utiliza el formato de datos de Cocos2D")

Archivos de imagen (como **.png**) puede agregarse a TexturePacker mediante arrastrar y soltar ellos desde el Explorador de Windows en Windows o el buscador de Mac. TexturePacker actualiza automáticamente la vista previa de hoja de sprite cada vez que se agrega un archivo:

![](ccspritesheet-images/image8.png "TexturePacker actualiza automáticamente la vista previa de hoja de sprite cada vez que se agrega un archivo")

Para exportar una hoja de sprite, haga clic en el **hoja de publicación sprite** botón y seleccione una ubicación para la hoja de sprite. TexturePacker guardará un archivo .plist y un archivo de imagen.

Para usar los archivos resultantes, agregue el .png y .plist a un proyecto de CocosSharp. Para obtener información sobre cómo agregar archivos a los proyectos de CocosSharp, consulte el [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md). Una vez que se agregan los archivos, se pueden cargar en un `CCSpriteSheet` tal y como se mostró anteriormente en el código anterior:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>Consideraciones para el mantenimiento de una hoja de sprite TexturePacker

A medida que se desarrollan juegos, intérpretes pueden agregar, quitar o modificar el material gráfico. Cualquier cambio requiere una hoja de sprite actualizada. Las consideraciones siguientes pueden facilitar el mantenimiento de la hoja de sprite:

 - Mantener los archivos originales (los archivos que se usa para crear las hojas sprite) en una carpeta del proyecto y asegúrese de que se agregan al control de versiones. Estos archivos se necesitará para volver a crear la hoja de sprite cada vez que se realiza un cambio.
 - No agregue los archivos originales a Visual Studio para Mac/Visual Studio, o si se agregan, establezca el **acción de compilación** a **ninguno**. Si los archivos se agregan y tienen la específica de la plataforma **acción de compilación**, a continuación, innecesariamente aumentan el tamaño del archivo de la aplicación resultante.
 - Considere el uso de *inteligentes carpetas* en TexturePacker. Carpetas inteligentes agregan automáticamente cualquier imagen independiente a la hoja sprite. Esta característica puede ahorrar una gran cantidad de tiempo al desarrollo de juegos con un gran número de imágenes. 

    ![](ccspritesheet-images/image9.png "Esta característica puede ahorrar una gran cantidad de tiempo al desarrollo de juegos con un gran número de imágenes")
 - Esté atento en tamaños de textura de sprite hoja. Hardware teléfono más antiguo no admite tamaños de textura mayores que 2048 x 2048. Además, una imagen de 32 bits de 2048 x 2048 usa casi 17 MB de RAM: una cantidad significativa de memoria.
 - TexturePacker no incluye las carpetas en los nombres de sprite de forma predeterminada, por lo que son posibles conflictos de nombres. Es mejor decidir si desea incluir carpeta nombres o no al principio del desarrollo. Los juegos más grandes deben considerar el uso de nombres de carpeta para evitar conflictos. Para incluir las rutas de acceso de carpeta, haga clic en **mostrar avanzadas** en el **datos** sección y compruebe **nombre de la carpeta Prepend**. 

    ![](ccspritesheet-images/image10.png "Para incluir las rutas de acceso de carpeta, haga clic en Mostrar opciones avanzadas en la sección de datos y compruebe el nombre de la carpeta Prepend")

## <a name="summary"></a>Resumen

Esta guía describe cómo crear y usar el `CCSpriteSheet` clase. También se describe cómo construir archivos que se pueden cargar en `CCSpriteSheet` instancias mediante el programa de TexturePacker.

## <a name="related-links"></a>Vínculos relacionados

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [Demostración completa (ejemplo)](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
