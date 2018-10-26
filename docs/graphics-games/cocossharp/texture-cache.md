---
title: Textura de almacenamiento en caché mediante CCTextureCache
description: Clase de CCTextureCache de CocosSharp proporciona una manera estándar para organizar, caché y descargar el contenido. Resulta especialmente útil para grandes juegos que no caben por completo en la RAM, lo que simplifica el proceso de agrupación y desechar las texturas.
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 232363d6ce1cb93499716b2c1247c48403cf6cea
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117387"
---
# <a name="texture-caching-using-cctexturecache"></a>Textura de almacenamiento en caché mediante CCTextureCache

_Clase de CCTextureCache de CocosSharp proporciona una manera estándar para organizar, caché y descargar el contenido. Resulta especialmente útil para grandes juegos que no caben por completo en la RAM, lo que simplifica el proceso de agrupación y desechar las texturas._

La `CCTextureCache` clase es una parte esencial del desarrollo de juegos de CocosSharp. Uso de juegos de CocosSharp mayoría el `CCTextureCache` objeto, aunque no de forma explícita, usen internamente tantos métodos de CocosSharp un *compartido* caché de textura.

Esta guía se explica el `CCTextureCache` y por qué es importante para el desarrollo de juegos. En concreto trata:

 - ¿Por qué es importante de almacenamiento en caché de textura
 - Duración de textura
 - Uso de SharedTextureCache
 - La carga frente a la carga previa con AddImage diferida
 - Eliminación de texturas


## <a name="why-texture-caching-matters"></a>¿Por qué es importante de almacenamiento en caché de textura

Almacenamiento en caché de textura es una consideración importante en el desarrollo de juegos como carga de textura es una operación lenta y texturas requieren una cantidad significativa de memoria RAM en tiempo de ejecución.

Al igual que con cualquier operación de archivo, cargar las texturas desde el disco puede ser una operación costosa. Carga de textura puede tardar más tiempo si el archivo que se está cargando requiere procesamiento, por ejemplo, se descomprimen (como es el caso de imágenes png y jpg). Almacenamiento en caché de textura puede reducir el número de veces que la aplicación debe cargar archivos desde el disco.

Como se mencionó anteriormente, las texturas ocupan también una gran cantidad de memoria en tiempo de ejecución. Por ejemplo, una imagen de fondo de tamaño para la resolución de un iPhone 6 (1344 x 750) ocuparía 4 megabytes de RAM, incluso si el archivo PNG es de sólo algunos kilobytes de tamaño. Almacenamiento en caché de textura proporciona una manera de compartir las referencias de textura dentro de una aplicación y también una manera fácil de descargar todo el contenido al realizar la transición entre distintos Estados de juego.


## <a name="texture-lifespan"></a>Duración de textura

Las texturas de CocosSharp pueden conservarse en la memoria para toda la longitud de la ejecución de una aplicación o pueden ser corta duración. Para minimizar la memoria debería eliminar las texturas cuando ya no necesita una aplicación de uso. Por supuesto, esto significa que las texturas pueden eliminadas y vuelva a cargar en un momento posterior, que puede aumentar los tiempos de carga o afectar negativamente al rendimiento durante las cargas. 

A menudo la carga de textura requiere un equilibrio entre los tiempos de carga y uso de memoria y rendimiento en tiempo de ejecución. Juegos que usan una pequeña cantidad de memoria de textura pueden mantener todas las texturas en la memoria según sea necesario, pero los juegos de mayor tamaño posible que deba descargar texturas para liberar espacio.

El siguiente diagrama muestra un sencillo juego que carga las texturas, según sea necesario y los mantiene en memoria durante toda la longitud de la ejecución de:

![](texture-cache-images/image1.png "Este diagrama muestra un sencillo juego que carga las texturas, según sea necesario y los mantiene en memoria durante toda la longitud de la ejecución")

Las dos primeras barras representan las texturas que se necesitan de inmediato tras la ejecución del juego. Las siguientes tres barras representan las texturas para cada nivel, cargado según sea necesario.

Si el juego es grande bastante, cargaría finalmente suficiente texturas para rellenar toda la memoria RAM proporcionada por el dispositivo y el sistema operativo. Para resolver este problema, un juego puede descargar datos de textura cuando ya no es necesaria. Por ejemplo, el siguiente diagrama muestra un juego que descarga Level1Texture cuando ya no es necesario, a continuación, se carga Level2Texture del siguiente nivel. El resultado final es que solo tres de las texturas se mantienen en memoria en un momento dado: 

![](texture-cache-images/image2.png "El resultado final es que solo tres de las texturas se mantienen en memoria en un momento dado")


El diagrama anterior indica que el uso de memoria de textura puede reducirse si se descarga, pero esto puede requerir tiempos de carga adicional si decide un reproductor reproducir un nivel. También merece la pena tener en cuenta que las texturas UITexture y MainCharacter se cargan y no descargan nunca. Esto implica que se necesitan estas texturas en todos los niveles, por lo que siempre se mantienen en memoria. 


## <a name="using-sharedtexturecache"></a>Uso de SharedTextureCache

CocosSharp almacena en caché automáticamente las texturas al cargarlos a través de la `CCSprite` constructor. Por ejemplo el siguiente código crea solo una instancia de la textura:


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

CocosSharp automáticamente almacena en caché el `star.png` textura para evitar el costosa alternativa de crear numerosos idénticos `CCTexture2D` instancias. Esto se consigue mediante `AddImage` que se llama en un compartido `CCTextureCache` , específicamente instancia `CCTextureCache.SharedTextureCache.Shared`. Para comprender cómo la `SharedTextureCache` sirve podemos observar el código siguiente que es funcionalmente idéntico al llamar a la `CCSprite` constructor con un parámetro de cadena:


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` comprueba si el archivo de argumentos (en este caso `star.png`) ya se ha cargado. Si es así, se devuelve la instancia almacenada en caché. Si no, a continuación, se carga desde el sistema de archivos y una referencia a la textura se almacena internamente para posteriores `AddImage` llamadas. En otras palabras los `star.png` imagen solo se carga una vez y las llamadas subsiguientes no necesitan acceso de disco adicional o memoria de texturas adicionales.


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>La carga frente a la carga previa con AddImage diferida

`AddImage` permite escribir el mismo código si la textura solicitada ya está cargada o no. Esto significa que el contenido no se cargará hasta que sea necesario; Sin embargo, también Esto puede causar problemas de rendimiento en tiempo de ejecución debido a contenido impredecible cargar.

Por ejemplo, considere un juego donde puede actualizarse arma del Reproductor. Cuando actualiza, el arma y proyectiles visiblemente cambiará, lo que resulta en nuevas texturas que se va a usar. Si el contenido es la carga diferida, a continuación, las texturas asociadas armas actualizadas no se cargará inicialmente, sino que en un momento posterior si el Reproductor adquiere las actualizaciones. 

Esta carga de mediados del juego puede hacer que el juego para *pop*, que es una inmovilización breve pero apreciable en ejecución. Para evitar esto, el código puede predecir qué texturas pueden ser necesarios hasta la parte delantera y cargarlas previamente. Por ejemplo, la siguiente puede utilizarse para cargar previamente las texturas:


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

Esta carga previa puede dar lugar a en memoria desperdiciada y puede aumentar el tiempo de inicio. Por ejemplo, el Reproductor nunca realmente puede obtener una puesta en marcha representado por la `powerup3.png` de textura, por lo que se cargarán innecesariamente. Por supuesto, esto puede ser un costo necesario pagar evitar un posible pop en juego, por lo que es precargar contenido suele ser mejor si se ajusta a en la memoria RAM.


## <a name="disposing-textures"></a>Eliminación de texturas

Si un juego no requiere más memoria de textura que está disponible en la especificación mínima del dispositivo texturas no es necesario para eliminarse. Por otro lado, juegos de mayor tamaño posible que deba liberar memoria de textura para dejar espacio para el nuevo contenido. Por ejemplo, un juego puede usar una gran cantidad de memoria que almacena las texturas para un entorno. Si el entorno solo se usa en un nivel específico, a continuación, debe descargarse cuando finaliza el nivel.


### <a name="disposing-a-single-texture"></a>Eliminación de una textura única

En primer lugar quitar una textura única requiere llamar a la `Dispose` método y, a continuación, la eliminación manual de la `CCTextureCache`.

La continuación muestra cómo quitar completamente un sprite en segundo plano, junto con su textura de:


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

Puede ser eficaz disposing directamente las texturas al tratar con un número pequeño de las texturas, pero esto puede ser propensas a errores cuando se trabaja con grandes conjuntos de textura.

Las texturas pueden agruparse en personalizada (no compartida) `CCTextureCache` instancias para simplificar la limpieza de textura.

Por ejemplo, considere un ejemplo donde el contenido se cargó previamente con un nivel específico `CCTextureCache` instancia. El `CCTextureCache` instancia puede definirse en la clase que define el nivel (que puede ser un `CCLayer` o `CCScene`):


```csharp
CCTextureCache levelTextures; 
```

El `levelTextures` instancia, a continuación, se puede usar para cargar previamente las texturas específicas del nivel: 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

Por último cuando finaliza el nivel, las texturas pueden ser desechadas todas a la vez a través de la `CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

El método Dispose deshará todas las texturas internas, vaciar la memoria utilizada por estas texturas. Combinar `CCTextureCache.Shared` con un determinado modo de juego o nivel `CCTextureCache` instancia da como resultado algunas texturas conservar a través de toda la partida y algunos descargando el módulo como niveles de finalización, similar al diagrama presentado al principio de esta guía: 

![](texture-cache-images/image2.png "Combinación de CCTextureCache.Shared con un nivel o juego específicos del modo CCTextureCache resultados de la instancia en algunas texturas conservar a través de toda la partida y algunos descargando el módulo como niveles de finalización, similar al diagrama presentado al principio de esta guía")




## <a name="summary"></a>Resumen

Esta guía muestra cómo usar el `CCTextureCache` clase para el rendimiento de uso y tiempo de ejecución de la memoria de saldo. `CCTexturCache.SharedTextureCache` puede ser explícitamente o implícitamente se usa para cargar y almacenar en caché las texturas durante la vida de la aplicación, mientras `CCTextureCache` instancias pueden usarse para descargar las texturas para reducir el uso de memoria.

## <a name="related-links"></a>Vínculos relacionados

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/API/Type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
