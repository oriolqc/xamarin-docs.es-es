---
title: Control de varias resoluciones en CocosSharp
description: Esta guía muestra cómo trabajar con CocosSharp para desarrollar los juegos que se muestren correctamente en los dispositivos de distintas resoluciones.
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 577a3edbd106b6fba298b3ee5999265ef955f9dd
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>Control de varias resoluciones en CocosSharp

_Esta guía muestra cómo trabajar con CocosSharp para desarrollar los juegos que se muestren correctamente en los dispositivos de distintas resoluciones._

CocosSharp proporciona métodos para estandarizar las dimensiones del objeto en el juego, independientemente del número de píxeles en la pantalla del dispositivo físico. Tradicionalmente, juegos desarrollados para equipos y consolas de juegos podrían tener como destino una única solución. Los juegos más modernos y especialmente los juegos para dispositivos móviles, deben compilarse para dar cabida a una amplia variedad de dispositivos. Esta guía muestra cómo normalizar CocosSharp juegos sin tener en cuenta las características de la resolución de la pantalla física.

El comportamiento de resolución predeterminado de CocosSharp es para que coincida con los píxeles físicos con coordenadas del juego. En la tabla siguiente se muestra cómo varios dispositivos representaría un sprite de entorno de segundo plano con el ancho y alto de 368 x 240. La primera fila es técnicamente no un dispositivo real, pero en su lugar la representación esperada del objeto sprite, independientemente de la resolución del dispositivo:


| **Dispositivo** | **Resolución de pantalla** | **Captura de pantalla de ejemplo** |
|--- | --- |--- |
|Desfase|368 x 240 (con barras de color negras de relación de aspecto)| ![368 x 240 (con barras de color negras de relación de aspecto)](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960 x 640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920 x 1080](resolutions-images/image3.png) |

Este documento describe cómo usar CocosSharp para solucionar el problema que se muestra en la tabla anterior. Es decir, hablaremos sobre cómo hacer que cualquier dispositivo representar como se muestra en la primera fila, independientemente de la resolución de pantalla.


## <a name="working-with-setdesignresolutionsize"></a>Trabajar con SetDesignResolutionSize

El `CCScene` clase se utiliza normalmente como el contenedor raíz para todos los objetos visuales, pero también proporciona un método estático llamado `SetDesignResolutionSize` para especificar el tamaño predeterminado para todas las escenas. En otras palabras `SetDesignResolutionSize` método permite a los desarrolladores desarrollar los juegos que se mostrarán correctamente en una variedad de soluciones de hardware. Las plantillas de proyecto CocosSharp usan este método para establecer el tamaño del proyecto predeterminado a 1024 x 768, tal y como se muestra en el código siguiente:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```

Puede cambiar la `desiredWidth` y `desiredHeight` variables anteriores para modificar la presentación para que coincida con la resolución de su juego que desee. Por ejemplo, el código anterior podría modificarse como se indica a continuación para mostrar el fondo 368 x 240 como pantalla completa:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 368.0f;
    float desiredHeight = 240.0f;
    
    // This will set the world bounds to(0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```


## <a name="ccsceneresolutionpolicy"></a>CCSceneResolutionPolicy

`SetDesignResolutionSize` permite especificar la manera en que la ventana del juego se ajusta a la resolución que desee. Las secciones siguientes muestran cómo se muestra una imagen de 500 x 500 con diferentes `CCSceneResolutonPolicy` valores pasados a la `SetDesignResolutionSize` método. Los valores siguientes se proporcionan con el `CCSceneResolutionPolicy` enum:

 - `ShowAll` : Muestra la resolución solicitada toda, mantener la relación de aspecto.
 - `ExactFit` : Muestra la resolución solicitada toda, pero no se mantiene la relación de aspecto.
 - `FixedWidth` : Muestra todo el ancho solicitado, mantener la relación de aspecto.
 - `FixedHeight` : Muestra el alto solicitado, mantener la relación de aspecto.
 - `NoBorder` : Muestra el alto o el ancho completo, mantiene la relación de aspecto. Si la relación de aspecto del dispositivo es mayor que la relación de aspecto de la resolución que desee, a continuación, se muestra todo el ancho. Si la relación de aspecto del dispositivo es inferior a la relación de aspecto de la resolución que desee, a continuación, se muestra el alto.
 - `Custom` : La presentación depende del `Viewport` propiedad del elemento actual `CCScene`.

Todas las capturas de pantalla se producen en la resolución de iPhone 4s (960 x 640) en orientación horizontal y usar esta imagen:

![](resolutions-images/image4.png "Todas las capturas de pantalla se producen en iPhone 4s resolución 960 x 640 en orientación horizontal y usar esta imagen")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` Especifica que la resolución del juego completa estará visible en la pantalla, pero puede mostrar *formato letterbox* (barras de color negras) para ajustarse a diferentes relaciones de aspecto. Esta directiva se usa habitualmente como garantiza que toda la vista de juego se mostrará en la pantalla sin ninguna distorsión.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Formato letterbox está visible a la izquierda y derecha de la imagen para tener en cuenta la relación de aspecto física que se va a más amplio que la resolución que desee:

![](resolutions-images/image5.png "Formato letterbox está visible a la izquierda y derecha de la imagen para tener en cuenta la relación de aspecto física que se va a más amplio que la resolución que desee")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` Especifica que la resolución del juego completa será visible en la pantalla con ningún formato letterbox. El área visible podría distorsionarse (relación de aspecto no se puede mantener) según la relación de aspecto de hardware.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

Ningún formato letterbox está visible, pero dado que la resolución del dispositivo es rectangular se distorsiona la vista de juego:

![](resolutions-images/image6.png "Ningún formato letterbox está visible, pero dado que la resolución del dispositivo es rectangular se distorsiona la vista de juego")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` Especifica que el ancho de la vista coincidirá con el valor de ancho pasado a `SetDesignResolutionSize`, pero el alto visible está sujeto a la relación de aspecto del dispositivo físico. El valor del alto pasado a `SetDesignResolutionSize` se omite porque se calculará en tiempo de ejecución en función de la relación de aspecto del dispositivo físico. Esto significa que el alto calculado puede ser menor que el alto deseado (lo que resulta en partes de la vista de juego está fuera de la pantalla) o el alto calculado puede ser mayor que el alto deseado (que da lugar a mayor del que se muestra la vista de juego). Puesto que esto puede producir en más del juego se muestra, a continuación, puede aparecer como si se ha producido el formato letterbox; Sin embargo, el espacio adicional no será necesariamente negro si cualquier objeto visual aparece en él. 

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

El iPhone 4s tiene una relación de aspecto de 3:2, por lo que el alto calculado es aproximadamente 333 unidades:

![](resolutions-images/image7.png "El iPhone 4s tiene una relación de aspecto de 3:2, por lo que el alto calculado es aproximadamente 333 unidades")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

Conceptualmente, `FixedHeight` se comporta de forma similar a `FixedWidth` : el juego seguirán el valor del alto pasado a `SetDesignResolutionSize,` pero calculará el ancho en tiempo de ejecución en función de la resolución física. Como se mencionó anteriormente, esto significa que el ancho mostrado menor o mayor que el ancho deseado, lo que parte de los juegos que se están listos para pantalla o más el juego que se muestran, respectivamente.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

Puesto que la captura de pantalla siguiente se creó con la aplicación que se ejecuta en modo horizontal, el ancho calculado es mayor que 500 – 750 específicamente. Esta directiva mantiene el valor 0 X alineada a la izquierda, por lo que la resolución adicional es visible en el lado derecho de la pantalla.

![](resolutions-images/image8.png "Esta directiva mantiene el valor 0 X alineada a la izquierda, por lo que la resolución adicional es visible en el lado derecho de la pantalla")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` intenta mostrar la aplicación con ningún formato letterbox mientras se mantiene la relación de aspecto original (sin distorsión). Si la relación de aspecto de la resolución solicitada coincide con la relación de aspecto físico del dispositivo, no se producirá ningún recorte. Si no coinciden con relaciones de aspecto, recorte, a continuación, se producirá.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

La captura de pantalla siguiente muestra las partes superior e inferior de la pantalla que se recorta, mientras que se muestran todos los 500 píxeles de ancho de pantalla:

![](resolutions-images/image9.png "Esta captura de pantalla muestra las partes superior e inferior de la pantalla que se recorta, mientras que se muestran todos los 500 píxeles de ancho de presentación.")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` permite que cada uno de ellos `CCScene` para especificar su propio ventanilla personalizado en relación con la resolución especificada en `SetDesignResolutionSize`.

Define plano sus `Viewport` propiedad mediante la creación de un `CCViewport`, que a su vez se construye con un `CCRect`. Para obtener más información sobre `CCViewport` y `CCRect` consulte la [documentación de la API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/). En el contexto de creación de un `CCViewport` la `CCRect` los parámetros del constructor especifican (en orden):

 - x: la cantidad de desplazamiento horizontal de la ventanilla, donde cada unidad representa un ancho de pantalla completa. Por ejemplo, con un valor de .5f resultados de la escena desplazada a la derecha hasta la mitad del ancho de la pantalla.
 - y: cantidad de desplazamiento vertical de la ventanilla, donde cada unidad representa un alto de pantalla completa. Por ejemplo, con un valor de .5f resultados de la escena que se desplazan hacia abajo a la mitad del alto de pantalla.
 - ancho: la parte horizontal que debe ocupar la escena. Por ejemplo, con un valor de 1 / 3.0f da como resultado de la escena horizontalmente que ocupan una tercera parte de la pantalla.
 - alto: la parte vertical que debe ocupar la escena. Por ejemplo, con un valor de 1 / 3.0f da como resultado de la escena verticalmente que ocupan una tercera parte de la pantalla.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.Custom);
...
float horizontalDisplayPortion = 2 / 3.0f;
float verticalDisplayPortion = 1 / 2.0f;
float displayOffsetInScreenWidths = 0;
float displayOffsetInScreenHeights = .5f;
var rectangle = new CCRect (
    displayOffsetInScreenWidths, 
    displayOffsetInScreenHeights, 
    horizontalDisplayPortion, 
    verticalDisplayPortion);
scene.Viewport = new CCViewport (rectangle);
```

El código anterior genera lo siguiente:

![](resolutions-images/image10.png "El código anterior, el resultado en esta captura de pantalla")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

El `DefaultTexelToContentSizeRatio` simplifica utilizando texturas de mayor resolución en dispositivos con pantallas de mayor resolución. En concreto, esta propiedad permite juegos utilizar los activos de mayor resolución sin necesidad de cambiar el tamaño o posición de los elementos visuales. 

Por ejemplo, un juego puede incluir un recurso de imagen para un carácter de juego que es 200 píxeles de alto y la pantalla del juego (tal y como especifica `SetDesignResolutionSize`) puede ser 400 píxeles de alto. En este caso, el carácter ocupará la mitad de la pantalla. Sin embargo, si un recurso de píxeles de altura 400 se utilizaron para el carácter para dispositivos de mayor resolución, el carácter ocupa toda la altura de la pantalla. Si el objetivo es mantener el carácter del mismo tamaño para aprovechar las ventajas de los dispositivos de resolución superior, algún código adicional es necesario para mantener el sprite de carácter en la mitad del alto de la pantalla.

El ejemplo presentado anteriormente representa un problema común de desarrollo de juegos: adición de activos de gran tamaño sin requerir que el programador realizar el cambio de tamaño en cada elemento visual según la resolución del dispositivo. `DefaultTexelToContentSizeRatio` es una propiedad global que se utiliza para cambiar el tamaño de los elementos visuales. Este valor cambia el tamaño de todos los elementos visuales de un tipo determinado por el valor asignado.

Esta propiedad está presente en las clases siguientes: 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

El CocosSharp de Visual Studio para el conjunto de plantillas de Mac `CCSprite.DefaultTexelToContentSizeRatio` según el ancho del dispositivo como un ejemplo de cómo se puede usar. El código siguiente se encuentra en `CCApplicationDelegate.ApplicationDidFinishLaunching`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
           
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```


### <a name="defaulttexeltocontentsizeratio-example"></a>Ejemplo de DefaultTexelToContentSizeRatio

Para ver cómo `DefaultTexelToContentSizeRatio` afecta el tamaño del objeto visual de elementos, considere el código presentado anteriormente:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Lo que dará como resultado en la siguiente imagen con una textura de 500 x 500:

![](resolutions-images/image5.png "Lo que producirá esta imagen con una textura de 500 x 500")

IPad que retina se ejecuta en una resolución de 2048 x 1536 física. Esto significa que el juego tal como se muestra anteriormente queda 500 píxeles sobre 1536 píxeles físicos. Juegos pueden aprovechar las ventajas de esta resolución adicional mediante la creación de lo que se conoce normalmente como *hd* activos: activos que están diseñados para ejecutarse en las pantallas de resolución superior. Por ejemplo, este juego podría utilizar una versión de hd de esta textura un tamaño de 1000 x 1000. Sin embargo, con la imagen más grande, se crearán los `CCSprite` que tiene un ancho y alto de unidades de 1000. Puesto que nuestro juego siempre mostrará 500 unidades (debido a la `SetDesignResolutioSize` llamar a), nuestro sprite 1000 x 1000 sería demasiado grande (muestra solo la parte inferior izquierda de ella):

![](resolutions-images/image11.png "Puesto que el juego siempre mostrará 500 unidades debido a la llamada SetDesignResolutioSize, el sprite 1000 x 1000 sería demasiado grande que muestra solo la parte inferior izquierda de la misma")

Podemos establecer `CCSprite.DefaultTexelToContentSizeRatio` para tener en cuenta la textura de 1000 x 1000 son dos veces como ancho y alto que la textura de 500 x 500 original:


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

Ahora Si ejecutamos el juego la textura de 1000 x 1000 estará totalmente visible:

![](resolutions-images/image12.png "Ahora Si ejecutamos el juego la textura de 1000 x 1000 estará totalmente visible")


### <a name="defaulttexeltocontentsizeratio-details"></a>Detalles de DefaultTexelToContentSizeRatio

El `DefaultTexelToContentSizeRatio` propiedad es `static,` lo que significa que todos los objetos Sprite en la aplicación compartirán el mismo valor. Es el enfoque típico para juegos con realizados para diferentes resoluciones de activos para que contenga un conjunto completo de activos para cada categoría de resolución. De manera predeterminada el CocosSharp de Visual Studio para las plantillas de Mac proporciona **ld** y **hd** carpetas para los activos, que podría ser útiles para admitir dos conjuntos de texturas de juegos. Puede ser una carpeta de contenido de ejemplo con contenido similar:

![](resolutions-images/image13.png "Puede parecer a una carpeta de contenido de ejemplo con contenido")

Observe que la plantilla predeterminada también incluye código para agregar de forma condicional a la aplicación `ContentSearchPaths`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```

Esto significa que la aplicación buscará archivos en las rutas de acceso según si se está ejecutando en modo de resolución normales o de alta resolución. Por ejemplo, si la **hd** y **ld** las carpetas contienen un archivo denominado **background.png** , a continuación, el código siguiente se ejecute y seleccione el archivo correcto para la resolución:


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>Resumen

En este artículo se explica cómo crear juegos que se muestren correctamente, independientemente de la resolución del dispositivo. Muestra ejemplos del uso de diferentes `CCSceneResolutionPolicy` valores para cambiar el tamaño del juego según la resolución del dispositivo. También proporciona un ejemplo de cómo `DefaultTexelToContentSizeRatio` puede usarse para dar cabida a varios conjuntos de contenido sin necesidad de elementos visuales para cambiar de tamaño individualmente.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción CocosSharp](~/graphics-games/cocossharp/index.md)
- [Documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
