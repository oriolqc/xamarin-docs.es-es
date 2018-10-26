---
title: Mejorar la velocidad de fotogramas con CCSpriteSheet
description: CCSpriteSheet proporciona funcionalidad para combinar y el uso de muchos archivos de imagen en una textura. Reducir recuento de textura puede mejorar los tiempos de carga de un juego y velocidad de fotogramas.
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: eab6153653a8c8df2068aaaf879d84d35473c541
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123574"
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>Mejorar la velocidad de fotogramas con CCSpriteSheet

_CCSpriteSheet proporciona funcionalidad para combinar y el uso de muchos archivos de imagen en una textura. Reducir recuento de textura puede mejorar los tiempos de carga de un juego y velocidad de fotogramas._

Muchos juegos exigen los esfuerzos de optimización para ejecutarse sin problemas y cargar rápidamente en hardware móvil. La `CCSpriteSheet` clase puede ayudar a resolver muchos problemas comunes de rendimiento detectados por los juegos de CocosSharp. Esta guía trata problemas comunes de rendimiento y cómo abordarlos utilizando la `CCSpriteSheet` clase.


## <a name="what-is-a-sprite-sheet"></a>¿Qué es una hoja de sprites?

Un *hoja de sprites*, lo que puede también se conoce como un *atlas de texturas*, es una imagen que combina varias imágenes en un archivo. Esto puede mejorar el rendimiento en tiempo de ejecución, así como tiempos de carga de contenido.

Por ejemplo, la siguiente imagen es una hoja de sprites simple creada por tres imágenes independientes. Las imágenes individuales pueden tener cualquier tamaño y la hoja de sprites resultante no es necesaria que se debe rellenar completamente:

![](ccspritesheet-images/image1.png "Las imágenes individuales pueden tener cualquier tamaño y la hoja de sprites resultante no es necesaria que se debe rellenar por completo")


### <a name="render-states"></a>Estados de representación

Objetos visuales de CocosSharp (como `CCSprite`) simplifican el código de representación a través de código de representación API gráfica tradicional como MonoGame u OpenGL, que requieren la creación de búferes de vértices (como se describe en el [dibujar gráficos en 3D con Vértices en MonoGame](~/graphics-games/monogame/3d/part2.md) guía). A pesar de su simplicidad, no elimina el costo de la configuración de CocosSharp *Estados de representación*, que son el número de veces que debe cambiar el código de representación texturas u otros Estados relacionados con la representación.

Código interno de CocosSharp representa cada elemento visual secuencialmente, recorriendo el árbol visual a partir del actual `CCScene`. Por ejemplo, considere la siguiente escena:

![](ccspritesheet-images/image2.png "Tenga en cuenta esta escena")

CocosSharp podría presentar las cuatro estrellas en secuencia:

![](ccspritesheet-images/image3.png "CocosSharp haría que las cuatro estrellas en secuencia")

Puesto que cada `CCSprite` usa la misma textura, CocosSharp puede agrupar todos los cuatro estrellas. Este código requiere sólo una representación estado (asignación de la textura de estrella) por fotograma. Este escenario es muy eficaz.

Por supuesto, muy pocos juegos usan solo una imagen. La siguiente escena presenta un gráfico del planeta:

![](ccspritesheet-images/image4.png "Esta escena presenta un gráfico del planeta")

Lo ideal es que todos los sprites mediante una imagen en primer lugar (por ejemplo, las estrellas) y, a continuación, el resto de los sprites de la otra imagen (el planeta) debería dibujar de CocosSharp:

![](ccspritesheet-images/image5.png "Lo ideal es que debe dibujar de todos los sprites utilizando una imagen en primer lugar, como las estrellas, a continuación, el resto de los sprites de la otra imagen del planeta CocosSharp")

El orden anterior requiere dos Estados de representación: uno en la primera de ellas estrella, del primer planeta.

Si todos los `CCSprite` instancias son elementos secundarios del mismo `CCNode`, a continuación, CocosSharp optimizará el orden de dibujo para reducir los cambios de estado de representación. If, por otro lado, el `CCSprite` instancias se organizan de modo que no puede CocosSharp optimizar el procesamiento (por ejemplo, si forman parte del tipo de entidad diferente `CCNode` instancias), a continuación, el orden no puede ser óptimo. A continuación muestra el orden de draw posible peor, lo que resulta en cinco estados de representación:

![](ccspritesheet-images/image6.png "Esto muestra el orden de draw posible peor, lo que resulta en cinco estados de representación")

Estados de representación pueden ser difíciles optimizar porque el orden del dibujo debe obedecer el árbol visual de `CCNode` instancias. Este árbol a menudo está estructurado para que sea fácil trabajar con (por ejemplo, las entidades que contiene a sus elementos secundarios visuales) u organizan debido a la presentación visual deseada tal como se define por un artista.

Por supuesto, la situación ideal es tener un estado de representación única, a pesar de tener varias imágenes. Juegos de CocosSharp pueden hacerlo mediante la combinación de todas las imágenes en un único archivo y, después, cargar ese archivo (junto con sus que acompaña a **.plist** archivo) en un `CCSpriteSheet`. Mediante el `CCSpriteSheet` clase resulta aún más importante para juegos que tiene un gran número de imágenes, o que tienen muy complicado diseños. 

### <a name="load-times"></a>Tiempos de carga

Combinar varias imágenes en un archivo, también mejora tiempos de carga de un juego para una serie de motivos:

 - Combinar varias imágenes en un único archivo puede reducir el número total de píxeles que se usan a través de eficaces
 - Cargar menos archivos, habrá menos sobrecarga de cada archivo, como analizar encabezados .png
 - Cargar menos archivos requiere menos tiempo, lo que es importante para los medios basadas en disco como DVDs y unidades de disco duro del equipo tradicional de búsqueda

## <a name="using-ccspritesheet-in-code"></a>Uso de CCSpriteSheet en código

Para crear un `CCSpriteSheet` instancia, el código debe proporcionar una imagen y un archivo que define las regiones de la imagen que se usará para cada fotograma. Se puede cargar la imagen como un **.png** o **.xnb** archivo (si usa el [canalización de contenido](~/graphics-games/cocossharp/content-pipeline/index.md)). El archivo de definición de los marcos es un **.plist** archivo que se puede crear a mano o *TexturePacker* (que explicaremos más adelante).

La aplicación de ejemplo que [puede descargarse aquí](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/), crea el `CCSpriteSheet` desde un **.png** y **.plist** archivo utilizando el código siguiente:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

Una vez cargado, el `CCSpriteSheet` contiene un `List` de `CCSpriteFrame` instancias: cada instancia que corresponde a una de las imágenes de origen utilizadas para crear toda la hoja. En el caso de los **SpriteSheetDemo** proyecto, el `CCSpriteSheet` contiene tres imágenes. El **.plist** puede inspeccionar el archivo en Visual Studio para Mac o en cualquier editor de texto para ver las imágenes que están disponibles. Si observamos el **.plist** archivo en un editor de texto, podemos ver los tres marcos (secciones se omite para hacer hincapié en los nombres de claves):


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

Podemos usar el `Find` método buscar marcos por nombre, como se indica a continuación (se omite el código para hacer hincapié en `CCSpriteFrame` uso):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

Puesto que la `CCSprite` constructor puede aceptar un `CCSpriteFrame` parámetro, el código nunca tiene que investigar los detalles de la `CCSpriteFrame`, como qué textura utiliza, o la región de la imagen en la hoja de sprites maestro.


## <a name="creating-a-sprite-sheet-plist"></a>Creación de un plist de la hoja de sprites

El archivo .plist es un archivo basado en xml, que se puede crear y editar a mano. De forma similar, los programas de edición de imágenes pueden utilizarse para combinar varios archivos en un archivo de mayor tamaño. Desde la creación y el mantenimiento de las hojas de duendecillos pueden ser muy lentos, veremos el programa TexturePacker que puede exportar los archivos en el formato de CocosSharp. TexturePacker ofrece una segunda oportunidad y una versión "Profesional" y está disponible para Windows y Mac OS. El resto de esta guía se puede seguir utilizando la versión gratuita. 

Puede ser TexturePacker [descargado desde el sitio Web TexturePacker](https://www.codeandweb.com/texturepacker). Cuando se abre, TexturePacker no tiene un proyecto cargado. La pantalla inicial permite agregar sprites, abrir proyectos recientes (si se han creado otros proyectos) y seleccione el formato que se usará para la hoja de sprites. CocosSharp utiliza el formato de datos Cocos2D:

![](ccspritesheet-images/image7.png "CocosSharp utiliza el formato de datos Cocos2D")

Archivos de imagen (como **.png**) pueden agregarse a TexturePacker mediante arrastrar y soltar ellos desde el Explorador de Windows en Windows o Finder de Mac. TexturePacker actualiza automáticamente la vista previa de hoja de sprites, cada vez que se agrega un archivo:

![](ccspritesheet-images/image8.png "TexturePacker actualiza automáticamente la vista previa de hoja de sprites, cada vez que se agrega un archivo")

Para exportar una hoja de sprites, haga clic en el **hoja de sprites de publicación** botón y seleccione una ubicación para la hoja de sprites. TexturePacker guardará un archivo .plist y un archivo de imagen.

Para usar los archivos resultantes, agregue tanto el .png y. plist a un proyecto CocosSharp. Para obtener información sobre cómo agregar archivos a los proyectos de CocosSharp, consulte el [BouncingGame guía](~/graphics-games/cocossharp/bouncing-game.md). Una vez que se agregan los archivos, se pueden cargar en un `CCSpriteSheet` tal como se mostró anteriormente en el código anterior:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>Consideraciones para mantener una hoja de sprites TexturePacker

Mientras se desarrollan juegos, los artistas pueden agregar, quitar o modificar imágenes. Cualquier cambio requiere una hoja de sprites actualizada. Las consideraciones siguientes pueden facilitar el mantenimiento de la hoja de sprite:

 - Mantener los archivos originales (los archivos que se usa para crear las hojas de duendecillos) en una carpeta en el proyecto y asegúrese de que se agregan al control de versiones. Estos archivos se necesitará volver a crear la hoja de sprites, cada vez que se realiza un cambio.
 - No agregue los archivos originales a Visual Studio para Mac o Visual Studio, o si se agregan, establezca el **acción de compilación** a **ninguno**. Si los archivos se agregan y tienen el específico de la plataforma **acción de compilación**, a continuación, aumentará innecesariamente el tamaño del archivo de la aplicación resultante.
 - Considere el uso de *inteligente carpetas* en TexturePacker. Carpetas inteligentes agrega automáticamente cualquier imagen independiente a la hoja de sprites. Esta característica puede ahorrar mucho tiempo al desarrollo de juegos con un gran número de imágenes. 

    ![](ccspritesheet-images/image9.png "Esta característica puede ahorrar mucho tiempo al desarrollo de juegos con un gran número de imágenes")
 - Esté atento en tamaños de textura de hoja de sprites. Hardware más antiguo teléfono no es compatible con el tamaño de textura mayor que 2048 x 2048. Además, una imagen de 32 bits de 2048 x 2048 usa casi 17 MB de RAM: una cantidad significativa de memoria.
 - TexturePacker no incluye las carpetas en los nombres de sprite de forma predeterminada, por lo que son posibles conflictos de nombres. Es mejor decidir si debe incluir la carpeta de nombres o no al principio del desarrollo. Juegos de mayor tamaño deben considerar el uso de los nombres de carpeta para evitar conflictos. Para incluir las rutas de acceso de carpeta, haga clic en **Mostrar opciones avanzadas** en el **datos** sección y Active **nombre de la carpeta Prepend**. 

    ![](ccspritesheet-images/image10.png "Para incluir las rutas de acceso de carpeta, haga clic en Mostrar opciones avanzadas en la sección de datos y compruebe el nombre de la carpeta anteponer")

## <a name="summary"></a>Resumen

Esta guía explica cómo crear y usar el `CCSpriteSheet` clase. También se describe cómo construir archivos que se pueden cargar en `CCSpriteSheet` instancias mediante el programa TexturePacker.

## <a name="related-links"></a>Vínculos relacionados

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [Demostración completa (ejemplo)](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
