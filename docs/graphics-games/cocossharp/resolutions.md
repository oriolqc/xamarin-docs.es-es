---
title: Control de varias resoluciones en CocosSharp
description: Esta guía muestra cómo trabajar con CocosSharp para desarrollar juegos que se muestren correctamente en los dispositivos de distintas resoluciones.
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 6803dc2668b89ee2d037da8b34e202191dd5465d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61307819"
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>Control de varias resoluciones en CocosSharp

_Esta guía muestra cómo trabajar con CocosSharp para desarrollar juegos que se muestren correctamente en los dispositivos de distintas resoluciones._

CocosSharp proporciona métodos para estandarizar las dimensiones del objeto en el juego, independientemente del número de píxeles en pantalla de un dispositivo físico. Tradicionalmente, juegos desarrollados para PC y consolas de juegos podrían tener como destino una única solución. Los juegos modernos y especialmente los juegos para dispositivos móviles, deben compilarse para dar cabida a una amplia variedad de dispositivos. Esta guía muestra cómo estandarizar CocosSharp juegos independientemente de las características de la resolución de la pantalla física.

El comportamiento de resolución predeterminado de CocosSharp es para que coincida con píxeles físicos con coordenadas en juego. En la tabla siguiente se muestra cómo varios dispositivos podría presentar un sprite de entorno en segundo plano con el ancho y alto de 368 x 240. La primera fila es técnicamente no un dispositivo real, pero en su lugar la representación esperada del sprite, independientemente de la resolución del dispositivo:


| **Device** | **Resolución de pantalla** | **Captura de pantalla de ejemplo** |
|--- | --- |--- |
|Presentación deseado|368 x 240 píxeles (con barras negras para la relación de aspecto)| ![368 x 240 píxeles (con barras negras para la relación de aspecto)](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960x640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920x1080](resolutions-images/image3.png) |

Este documento describe cómo usar CocosSharp para corregir el problema que se muestra en la tabla anterior. Es decir, hablaremos sobre cómo hacer que cualquier dispositivo representar como se muestra en la primera fila, independientemente de la resolución de pantalla.


## <a name="working-with-setdesignresolutionsize"></a>Trabajar con SetDesignResolutionSize

El `CCScene` clase se utiliza normalmente como el contenedor raíz para todos los objetos visuales, pero también proporciona un método estático llamado `SetDesignResolutionSize` para especificar el tamaño predeterminado para todas las escenas. En otras palabras `SetDesignResolutionSize` método permite a los desarrolladores desarrollar juegos que se mostrarán correctamente en una variedad de resoluciones de hardware. Las plantillas de proyecto CocosSharp usan este método para establecer el tamaño predeterminado del proyecto a 1024 x 768, como se muestra en el código siguiente:


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

Puede cambiar el `desiredWidth` y `desiredHeight` variables anteriores para modificar la pantalla para que coincida con la resolución que desee de su juego. Por ejemplo, se podría modificar el código anterior como sigue para mostrar el fondo de 368 x 240 píxeles como pantalla completa:


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

`SetDesignResolutionSize` nos permite especificar cómo se ajusta la ventana del juego a la resolución que desee. Las secciones siguientes muestran cómo se muestra una imagen de 500 x 500 con diferentes `CCSceneResolutonPolicy` valores pasados a la `SetDesignResolutionSize` método. Los valores siguientes se proporcionan mediante el `CCSceneResolutionPolicy` enum:

 - `ShowAll` : Muestra la resolución solicitada toda, mantener la relación de aspecto.
 - `ExactFit` : Muestra la resolución solicitada toda, pero no mantiene la relación de aspecto.
 - `FixedWidth` : Muestra todo el ancho solicitado, mantener la relación de aspecto.
 - `FixedHeight` : Muestra el alto solicitado, mantener la relación de aspecto.
 - `NoBorder` : Muestra el alto o ancho completo, mantiene la relación de aspecto. Si la relación de aspecto del dispositivo es mayor que la relación de aspecto de la resolución que desee, a continuación, se muestra todo el ancho. Si la relación de aspecto del dispositivo es menor que la relación de aspecto de la resolución que desee, a continuación, se muestra el alto.
 - `Custom` : La visualización depende de la `Viewport` propiedad del elemento actual `CCScene`.

Todas las capturas de pantalla se producen en iPhone 4s resolución (960 x 640) en orientación horizontal y usar esta imagen:

![](resolutions-images/image4.png "Todas las capturas de pantalla se producen en la resolución de iPhone 4s 960 x 640 en orientación horizontal y usar esta imagen")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` Especifica que la resolución del juego completa estará visible en pantalla, pero es posible que muestre *formato letterbox* (barras negras) para ajustar para relaciones de aspecto diferentes. Esta directiva se usa habitualmente como lo que garantiza que toda la vista de juego se mostrará en pantalla sin ninguna distorsión.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Amplía la pantalla está visible a la izquierda y derecha de la imagen para tener en cuenta la relación de aspecto física que se va a más ancha que la resolución que desee:

![](resolutions-images/image5.png "Amplía la pantalla está visible a la izquierda y derecha de la imagen para tener en cuenta la relación de aspecto física que se va a más ancha que la resolución que desee")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` Especifica que la resolución del juego completa será visible en pantalla con ningún formato letterbox. El área visible podría distorsionarse (relación de aspecto no se puede mantener) según la relación de aspecto de hardware.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

Ningún formato letterbox está visible, pero dado que la resolución del dispositivo es rectangular se distorsiona la vista de juego:

![](resolutions-images/image6.png "Ningún formato letterbox está visible, pero dado que la resolución del dispositivo es rectangular se distorsiona la vista de juego")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` Especifica que el ancho de la vista coincidirá con el valor de ancho pasado a `SetDesignResolutionSize`, pero el alto visible está sujeta a la relación de aspecto del dispositivo físico. El valor del alto pasando a `SetDesignResolutionSize` se omite porque se calculará en tiempo de ejecución según la relación de aspecto del dispositivo físico. Esto significa que el alto calculado puede ser menor que el alto deseado (lo que da como resultado partes de la vista de juego que se está fuera de la pantalla) o el alto calculado puede ser mayor que el alto deseado (lo que resulta en más de la vista de juego que se muestra). Puesto que esto puede producir más del juego que se muestra, a continuación, puede parecer como si se ha producido el formato letterbox; Sin embargo, el espacio adicional no será necesariamente negro si aparece algún objeto visual. 

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4s tiene una relación de aspecto de 3:2, por lo que el alto calculado es de aproximadamente 333 unidades:

![](resolutions-images/image7.png "IPhone 4s tiene una relación de aspecto de 3:2, por lo que el alto calculado es de aproximadamente 333 unidades")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

Conceptualmente, `FixedHeight` se comporta de forma similar a `FixedWidth` : el juego seguirán el valor alto pasado a `SetDesignResolutionSize,` pero calculará el ancho en tiempo de ejecución según la resolución física. Como se mencionó anteriormente, esto significa que el ancho mostrado menor o mayor que el ancho deseado, lo que resulta en la parte del juego que se de descuento en pantalla o en más del juego que se muestran, respectivamente.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

Puesto que la captura de pantalla siguiente se creó con la aplicación que se ejecuta en modo horizontal, el ancho calculado es mayor que 500 – 750 específicamente. Esta directiva mantiene el valor 0 X alineado a la izquierda, por lo que esta resolución adicional es visible en el lado derecho de la pantalla.

![](resolutions-images/image8.png "Esta directiva mantiene el valor 0 X alineado a la izquierda, por lo que esta resolución adicional es visible en el lado derecho de la pantalla")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` intenta mostrar la aplicación con ningún formato letterbox mientras mantiene la relación de aspecto original (sin distorsión). Si la relación de aspecto de la resolución solicitada coincide con la relación de aspecto físico del dispositivo, no se producirá ningún recorte. Si no coinciden con relaciones de aspecto, recorte, a continuación, se producirá.

Ejemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

Captura de pantalla siguiente muestra las partes superior e inferior de la pantalla que se recorta, mientras que se muestran todos los 500 píxeles de ancho de la pantalla:

![](resolutions-images/image9.png "Esta captura de pantalla muestra las partes superior e inferior de la pantalla que se recorta, mientras que se muestran todos los 500 píxeles de ancho de la pantalla")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` permite que cada `CCScene` para especificar su propia área de visualización personalizado en relación con la resolución especificada en `SetDesignResolutionSize`.

Definen escenas sus `Viewport` propiedad mediante la creación de un `CCViewport`, que a su vez se construye con un `CCRect`. Para obtener más información sobre `CCViewport` y `CCRect` vea el [documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/). En el contexto de la creación de un `CCViewport` el `CCRect` parámetros del constructor que especifique (en orden):

 - x: la cantidad de desplazamiento horizontal la ventanilla, donde cada unidad representa un ancho de pantalla completa. Por ejemplo, con un valor de .5f resultados de la escena desplazada a la derecha hasta la mitad del ancho de la pantalla.
 - y es la cantidad de desplazamiento verticalmente la ventanilla, donde cada unidad representa una altura de pantalla completa. Por ejemplo, con un valor de .5f resultados de la escena que se desplazan hacia abajo a la mitad del alto de la pantalla.
 - ancho: la parte horizontal que debe ocupar la escena. Por ejemplo, con un valor de 1 / 3.0f da como resultado de la escena horizontalmente ocupando un tercio de la pantalla.
 - alto: la parte vertical que debe ocupar la escena. Por ejemplo, con un valor de 1 / 3.0f da como resultado de la escena verticalmente ocupando un tercio de la pantalla.

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

El `DefaultTexelToContentSizeRatio` simplifica el uso de las texturas de una resolución mayor de dispositivos con pantallas de mayor resolución. En concreto, esta propiedad permite que los juegos utilizar los activos de mayor resolución sin necesidad de cambiar el tamaño o posición de elementos visuales. 

Por ejemplo, un juego puede incluir un recurso de material gráfico para un carácter de juego que es de 200 píxeles de alto y la pantalla de juego (tal y como especifica `SetDesignResolutionSize`) puede ser 400 píxeles de alto. En este caso, el carácter ocupará la mitad de la pantalla. Sin embargo, si un recurso de alto de píxel 400 se utilizaron para el carácter para dispositivos de resolución superior, el carácter ocuparían el alto de la pantalla. Si el objetivo es mantener el carácter del mismo tamaño para aprovechar las ventajas de los dispositivos de resolución superior, algún código adicional es necesario mantener el sprite del carácter en la mitad del alto de la pantalla.

El ejemplo presentado anteriormente representa un problema común de desarrollo de juegos: adición de activos de mayor tamaño sin requerir al desarrollador realizar el cambio de tamaño en cada elemento visual según la resolución del dispositivo. `DefaultTexelToContentSizeRatio` es una propiedad global que se utiliza para cambiar el tamaño de los elementos visuales. Este valor cambia el tamaño de todos los elementos visuales de un tipo determinado por el valor asignado.

Esta propiedad está presente en las clases siguientes: 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio para el conjunto de plantillas de Mac `CCSprite.DefaultTexelToContentSizeRatio` según el ancho del dispositivo como un ejemplo de cómo se puede usar. El siguiente código se encuentra en `CCApplicationDelegate.ApplicationDidFinishLaunching`:


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

Para ver cómo `DefaultTexelToContentSizeRatio` afecta el tamaño del objeto visual de los elementos, considere el código presentado anteriormente:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Lo que dará como resultado en la siguiente imagen con una textura de 500 x 500:

![](resolutions-images/image5.png "Lo que dará como resultado de esta imagen con una textura de 500 x 500")

IPad Retina se ejecuta con una resolución de 2048 x 1536 física. Esto significa que el juego, tal como se muestra anteriormente queda 500 píxeles en píxeles físicos de 1536. Juegos pueden sacar partido de esta resolución adicional mediante la creación de lo que se conoce normalmente como *hd* activos: activos que están diseñados para ejecutarse en pantallas de mayor resolución. Por ejemplo, este juego podría usar una versión de hd de esta textura un tamaño de 1000 x 1000. Sin embargo, con la imagen más grande daría como resultado la `CCSprite` que tiene un ancho y alto de 1000 unidades. Puesto que nuestro juego siempre mostrará 500 unidades (debido a la `SetDesignResolutioSize` llamar), entonces nuestro sprite 1000 x 1000 sería demasiado grande (muestra solo la parte inferior izquierda de ella):

![](resolutions-images/image11.png "Puesto que el juego siempre mostrará 500 unidades debido a la llamada SetDesignResolutioSize, el sprite 1000 x 1000 sería demasiado grande que muestra solo la parte inferior izquierda de la misma")

Podemos establecer `CCSprite.DefaultTexelToContentSizeRatio` para tener en cuenta la textura de 1000 x 1000, que se va a dos veces como anchos y altos como la textura original de 500 x 500:


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

Ahora si se ejecuta el juego será totalmente visible la textura de 1000 x 1000:

![](resolutions-images/image12.png "Ahora si se ejecuta el juego de la textura de 1000 x 1000 será totalmente visible")


### <a name="defaulttexeltocontentsizeratio-details"></a>Detalles de DefaultTexelToContentSizeRatio

El `DefaultTexelToContentSizeRatio` propiedad es `static,` lo que significa que todos los sprites de la aplicación compartirá el mismo valor. Es el enfoque típico para juegos con recursos realizados para diferentes resoluciones para que contenga un conjunto completo de activos para cada categoría de resolución. De forma predeterminada el CocosSharp de Visual Studio para las plantillas de Mac proporcionan **ld** y **hd** carpetas para los recursos, que sería útiles para juegos que admiten dos conjuntos de texturas. Puede ser una carpeta de contenido de ejemplo con contenido similar:

![](resolutions-images/image13.png "Puede ser una carpeta de contenido de ejemplo con contenido similar a")

Tenga en cuenta que la plantilla predeterminada también incluye código para agregar la aplicación condicionalmente `ContentSearchPaths`:


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

Esto significa que la aplicación buscará los archivos en las rutas de acceso según si se está ejecutando en modo de resolución normal o alta resolución. Por ejemplo, si la **hd** y **ld** carpetas contienen un archivo denominado **background.png** , a continuación, el código siguiente se ejecute y seleccione el archivo correcto para la resolución:


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>Resumen

En este artículo se explica cómo crear juegos que se muestren correctamente, independientemente de la resolución del dispositivo. Muestra ejemplos del uso de diferentes `CCSceneResolutionPolicy` valores para cambiar el tamaño del juego según la resolución del dispositivo. También proporciona un ejemplo de cómo `DefaultTexelToContentSizeRatio` se puede usar para dar cabida a varios conjuntos de contenido sin necesidad de elementos visuales para cambiar de tamaño individualmente.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción de CocosSharp](~/graphics-games/cocossharp/index.md)
- [Documentación de API de CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
