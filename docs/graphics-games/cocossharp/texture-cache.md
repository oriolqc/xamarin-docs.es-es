---
title: Textura, almacenamiento en caché mediante CCTextureCache
description: Clase de CCTextureCache del CocosSharp proporciona un método estándar para organizar, caché y descargar contenido. Es especialmente útil para grandes juegos que no caben completamente en RAM, simplificando el proceso de agrupación y eliminación de las texturas.
ms.prod: xamarin
ms.assetid: 1B5F3F85-9E68-42A7-B516-E90E54BA7102
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: c217d8a935ae971aab472b05968c0251366362b2
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783693"
---
# <a name="texture-caching-using-cctexturecache"></a>Textura, almacenamiento en caché mediante CCTextureCache

_Clase de CCTextureCache del CocosSharp proporciona un método estándar para organizar, caché y descargar contenido. Es especialmente útil para grandes juegos que no caben completamente en RAM, simplificando el proceso de agrupación y eliminación de las texturas._

La `CCTextureCache` clase es una parte esencial del desarrollo de juegos de CocosSharp. Uso de juegos de CocosSharp mayoría el `CCTextureCache` objeto, incluso si no explícitamente, como muchos métodos de CocosSharp usan internamente un *compartido* caché de textura.

Esta guía se describe la `CCTextureCache` y por qué es importante para el desarrollo de juego. En concreto trata:

 - ¿Por qué cuestiones sobre el almacenamiento en caché de textura
 - Tiempo de vida de textura
 - Usar SharedTextureCache
 - Frente a una carga previa con AddImage de carga diferida
 - Eliminación de texturas


## <a name="why-texture-caching-matters"></a>¿Por qué cuestiones sobre el almacenamiento en caché de textura

Almacenamiento en caché de textura es una consideración importante en el desarrollo de juego como carga de textura es una operación que consume mucho tiempo y texturas requieren una gran cantidad de RAM en tiempo de ejecución.

Al igual que con cualquier operación de archivo, cargando texturas del disco puede ser una operación costosa. Carga de textura puede tardar más tiempo si el archivo que se está cargando requiere procesamiento, por ejemplo, se descomprimen (como es el caso de imágenes png y jpg). Almacenamiento en caché de textura puede reducir el número de veces que la aplicación debe cargar archivos desde el disco.

Como se mencionó anteriormente, las texturas también ocupan una gran cantidad de memoria en tiempo de ejecución. Por ejemplo, una imagen de fondo tienen un tamaño de la resolución de un iPhone 6 (1344 x 750) ocuparían 4 megabytes de memoria RAM: incluso si el archivo PNG es solo algunos kilobytes de tamaño. Almacenamiento en caché de textura proporciona una manera de compartir las referencias de textura dentro de una aplicación y también una manera fácil de descargar todo el contenido al realizar la transición entre los diferentes Estados de juegos.


## <a name="texture-lifespan"></a>Tiempo de vida de textura

Las texturas CocosSharp pueden conservarse en la memoria durante toda la longitud de la ejecución de una aplicación o pueden ser duración breve. Para minimizar la memoria debe eliminar una aplicación de uso de las texturas cuando ya no necesite. Por supuesto, esto significa que las texturas pueden eliminadas y vuelto a cargar en un momento posterior, que puede aumentar los tiempos de carga o afectar al rendimiento durante la carga. 

A menudo la carga de textura requiere un equilibrio entre los tiempos de carga y uso de memoria y rendimiento en tiempo de ejecución. Juegos que usan una pequeña cantidad de memoria de textura pueden mantener todas las texturas en la memoria según sea necesario, pero los juegos más grandes que necesite descargar texturas para liberar espacio.

El siguiente diagrama muestra un juego sencillo que carga las texturas según sea necesario y los mantiene en la memoria durante toda la longitud de la ejecución:

![](texture-cache-images/image1.png "Este diagrama muestra un juego sencillo que carga las texturas según sea necesario y los mantiene en la memoria durante toda la longitud de ejecución")

Las dos primeras barras representan las texturas que se necesitan inmediatamente tras la ejecución del juego. Las siguientes tres barras representan las texturas para cada nivel de carga según sea necesario.

Si el juego es grande finalmente lo suficientemente se cargaría suficiente texturas para rellenar toda la memoria RAM proporcionada por el dispositivo y el sistema operativo. Para resolver este problema, un juego puede descargar datos de textura cuando ya no es necesario. Por ejemplo, el siguiente diagrama muestra un juego que descarga Level1Texture cuando ya no es necesario, a continuación, se carga Level2Texture por el siguiente nivel. El resultado final es que sólo tres de las texturas se mantienen en la memoria en un momento dado: 

![](texture-cache-images/image2.png "El resultado final es que sólo tres de las texturas se mantienen en la memoria en un momento dado")


El diagrama anterior indica que se puede reducir el uso de memoria de textura descargar, pero esto puede requerir los tiempos de carga adicional si un jugador decida si desea reproducir un nivel. También merece la pena mencionar que las texturas UITexture y MainCharacter se cargan y no descargan nunca. Esto implica que se necesitan estas texturas en todos los niveles, por lo que siempre se mantienen en la memoria. 


## <a name="using-sharedtexturecache"></a>Usar SharedTextureCache

CocosSharp almacena automáticamente en caché las texturas al cargarlos a través de la `CCSprite` constructor. Por ejemplo el siguiente código crea solo una instancia de textura:


```csharp
for (int i = 0; i < 100; i++)
{
    CCSprite starSprite = new CCSprite ("star.png");
    starSprite.PositionX = i * 32;
    this.AddChild (starSprite);
} 
```

CocosSharp almacena automáticamente en caché la `star.png` textura para evitar el costosa alternativa de crear numerosas idénticos `CCTexture2D` instancias. Para ello, `AddImage` que se llama en un compartido `CCTextureCache` instancia, específicamente `CCTextureCache.SharedTextureCache.Shared`. Para comprender cómo la `SharedTextureCache` se utiliza podemos observar el siguiente código que es funcionalmente idéntico al llamar a la `CCSprite` constructor con un parámetro de cadena:


```

CCSprite starSprite = new CCSprite ();
 starSprite.Texture = CCTextureCache.SharedTextureCache.AddImage ("star.png");
```

`AddImage` comprueba si el archivo de argumentos (en este caso `star.png`) ya se ha cargado. Si es así, se devuelve la instancia almacenada en caché. Si no, a continuación, se carga desde el sistema de archivos y una referencia a la textura se almacena internamente para posteriores `AddImage` llamadas. Dicho de otro modo el `star.png` imagen solo se carga una vez y las llamadas subsiguientes no necesitan acceso adicional en el disco o memoria de textura adicionales.


## <a name="lazy-loading-vs-pre-loading-with-addimage"></a>Frente a una carga previa con AddImage de carga diferida

`AddImage` permite escribir el mismo código si la textura solicitada ya está cargada o no. Esto significa que el contenido no se cargará hasta que se necesite; Sin embargo, también Esto puede causar problemas de rendimiento en tiempo de ejecución debido a contenido imprevisible cargar.

Por ejemplo, considere un juego donde puede actualizarse arma del Reproductor. Cuando actualiza, el arma y proyectiles visiblemente cambiarán, lo que las texturas nuevo que se va a usar. Si el contenido es la carga diferida, a continuación, las texturas asociadas armas actualizados no se cargará inicialmente, sino que en un momento posterior si el Reproductor adquiere las actualizaciones. 

Para hacer que esta carga mid juega el juego para *pop*, que es una inmovilización breve pero perceptible en ejecución. Para evitar esto, el código puede predecir qué texturas pueden ser necesaria una copia de seguridad frente y cargar previamente en ellos. Por ejemplo, la siguiente puede utilizarse para cargar previamente las texturas:


```csharp
void PreLoadImages()
{
    var cache = CCTextureCache.SharedTextureCache;

    cache.AddImage ("powerup1.png");
    cache.AddImage ("powerup2.png");
    cache.AddImage ("powerup3.png");

    cache.AddImage ("enemy1.png");
    cache.AddImage ("enemy2.png");
    cache.AddImage ("enemy3.png");

    // pre-load any additional content here to 
    // prevent pops at runtime
} 
```

Esta carga previa puede dar lugar a pérdida memoria y puede aumentar el tiempo de inicio. Por ejemplo, el Reproductor puede obtener nunca realmente una puesta en marcha representado por la `powerup3.png` de textura, por lo que se cargarán innecesariamente. Por supuesto, esto puede ser un costo necesario para pagar para evitar un posible pop en juego, por lo que es normalmente lo más aconsejable precargar contenido si se ajusta en la memoria RAM.


## <a name="disposing-textures"></a>Eliminación de texturas

Si un juego no requiere más memoria de textura que está disponible en el dispositivo de especificación mínima texturas no es necesario van a eliminar. Por otro lado, los juegos más grandes que necesite liberar memoria de textura para dejar espacio para el contenido nuevo. Por ejemplo, un juego puede utilizar una gran cantidad de memoria que las texturas para un entorno de almacenamiento. Si el entorno solo se usa en un nivel concreto, a continuación, debe descargarse cuando finaliza el nivel.


### <a name="disposing-a-single-texture"></a>Elimine una textura única

Quitar una textura única, primero requiere llamar a la `Dispose` método y, a continuación, la eliminación manual de la `CCTextureCache`.

El siguiente código muestra cómo quitar completamente un sprite fondo junto con su textura de:


```csharp
void DisposeBackground()
{
    // Assuming this is called from a CCLayer:
    this.RemoveChild (backgroundSprite);

    CCTextureCache.SharedTextureCache.RemoveTexture (backgroundsprite.Texture);

    backgroundSprite.Texture.Dispose ();
} 
```

Desechar directamente las texturas puede ser eficaz al trabajar con un pequeño número de texturas, pero esto puede ser propensas a errores cuando se trabaja con conjuntos de textura más grandes.

Las texturas se pueden agrupar en personalizada (no compartida) `CCTextureCache` instancias para simplificar la limpieza de textura.

Por ejemplo, considere un ejemplo donde el contenido está precargado con un determinado nivel `CCTextureCache` instancia. El `CCTextureCache` instancia puede estar definida en la clase que define el nivel (lo que puede ser un `CCLayer` o `CCScene`):


```csharp
CCTextureCache levelTextures; 
```

El `levelTextures` , a continuación, puede utilizarse para cargar previamente las texturas específicos de nivel de instancia: 


```

void PreloadLevelTextures(CCApplication application)
{
    levelTextures = new CCTextureCache (application);

    levelTextures.AddImage ("Background.png");
    levelTextures.AddImage ("Foreground.png");
    levelTextures.AddImage ("Enemy1.png");
    levelTextures.AddImage ("Enemy2.png");
    levelTextures.AddImage ("Enemy3.png");

    levelTextures.AddImage ("Powerups.png");
    levelTextures.AddImage ("Particles.png");
} 
```

Por último, cuando finaliza el nivel, las texturas pueden estar eliminadas todas a la vez a través de la `CCTextureCache`:


```csharp
void EndLevel()
{
    levelTextures.Dispose ();
    // Perform any other end-level cleanup
} 
```

El método Dispose deshará todas las texturas internas, borrando la memoria utilizada por estas texturas. Combinar `CCTextureCache.Shared` con un nivel o juego específicos del modo `CCTextureCache` la instancia genera algunas texturas conservar a través de toda la partida y algunas se está descargando el módulo como niveles finalización, similar al diagrama presentan al principio de esta guía: 

![](texture-cache-images/image2.png "Combinación de CCTextureCache.Shared con un nivel o juego específicos de modo CCTextureCache resultados de la instancia en algunas texturas conservar a través de toda la partida y algunas se está descargando el módulo como niveles finalización, similar al diagrama presentan al principio de esta guía")




## <a name="summary"></a>Resumen

Esta guía muestra cómo utilizar la `CCTextureCache` clase para equilibrar el rendimiento de en tiempo de ejecución y de uso de memoria. `CCTexturCache.SharedTextureCache` puede especificar explícitamente o implícitamente se usa para cargar y almacenar en caché las texturas durante la vida de la aplicación, mientras `CCTextureCache` instancias pueden utilizarse para descargar las texturas para reducir el uso de memoria.

## <a name="related-links"></a>Vínculos relacionados

- [https://github.com/mono/CocosSharp](https://github.com/mono/CocosSharp)
- [/API/Type/CocosSharp.CCTextureCache/](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
