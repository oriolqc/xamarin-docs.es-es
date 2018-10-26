---
title: Rendimiento y efectos visuales con CCRenderTexture
description: CCRenderTexture permite a los desarrolladores mejorar el rendimiento de sus juegos de CocosSharp reduciendo las llamadas a draw y se puede usar para crear efectos visuales. Esta guía acompaña al ejemplo de CCRenderTexture para proporcionar un ejemplo práctico de cómo utilizar esta clase de forma eficaz.
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: conceptdev
ms.author: crdun
ms.openlocfilehash: 2e4d9a8fdefd0c543c29061da37098e443d1c092
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123120"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>Rendimiento y efectos visuales con CCRenderTexture

_CCRenderTexture permite a los desarrolladores mejorar el rendimiento de sus juegos de CocosSharp reduciendo las llamadas a draw y se puede usar para crear efectos visuales. Esta guía acompaña al ejemplo de CCRenderTexture para proporcionar un ejemplo práctico de cómo utilizar esta clase de forma eficaz._

La `CCRenderTexture` clase proporciona funcionalidad para representar varios objetos de CocosSharp a una textura única. Una vez creada, `CCRenderTexture` instancias pueden usarse para representar gráficos de forma eficaz y para implementar los efectos visuales. `CCRenderTexture` permite que varios objetos que van a representar en una textura solo una vez. A continuación, puede ser esa textura reutilizar cada fotograma, lo que reduce el número total de llamadas a draw.

Esta guía examina cómo usar el `CCRenderTexture` objeto para mejorar el rendimiento de las tarjetas de presentación en un juego de cartas que se pueden recopilar (CCG). También se muestra cómo `CCRenderTexture` puede usarse para hacer transparente una entidad completa. Esta guía hace referencia a la `CCRenderTexture` [proyecto de ejemplo](https://developer.xamarin.com/samples/mobile/CCRenderTexture/).

![](ccrendertexture-images/image1.png "Esta guía se hace referencia el proyecto de ejemplo CCRenderTexture")


## <a name="card--a-typical-entity"></a>Tarjeta de una entidad típica

Antes de ver cómo usar `CCRenderTexture` objeto, primero deberá familiarizarse nosotros mismos con el `Card` entidad que vamos a usar a lo largo de este proyecto para explorar el `CCRenderTexture` clase. El `Card` clase es una entidad típica, siguiendo el patrón de entidades que se describen en la [guía entidad](~/graphics-games/cocossharp/entities.md). La clase de tarjeta tiene todos sus componentes visuales (las instancias de `CCSprite` y `CCLabel`) aparece como campos:


```csharp
class Card : CCNode
{
    bool usesRenderTexture;
    List<CCNode> visualComponents = new List<CCNode>();
    CCSprite background;
    CCSprite colorIcon;
    CCSprite monsterSprite;
    CCLabel monsterNameDisplay;
    CCLabel hpDisplay;
    CCLabel descriptionDisplay;
```

Las instancias de la tarjeta pueden representarse mediante el uso de un `CCRenderTexture`, o mediante el dibujo de cada componente visual individualmente. Aunque cada componente es un objeto independiente, el `CCNode` relaciones jerárquicas de sistema que se usa en las entidades hace que el `Card` se comportan como un único objeto, por lo menos en su mayor parte. Por ejemplo, si un `Card` entidad se cambia de posición, cambia el tamaño o girar, a continuación, se ven afectados todos los objetos visuales contenidos para que la tarjeta aparezca como un único objeto. Para ver las tarjetas se comportan como un único objeto, podemos modificar la `GameLayer.AddedToScene` método para establecer el `useRenderTextures` variable `false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

El `GameLayer` código no mueve cada elemento visual de forma independiente, aún cada elemento visual en el `Card` entidad se coloca correctamente:

![](ccrendertexture-images/image1.png "El código GameLayer no mueve por separado cada elemento visual, pero cada elemento visual dentro de la entidad de la tarjeta esté colocado correctamente")

El ejemplo se codifica para exponer dos problemas que pueden producirse cuando cada componente visual representa a sí mismo:

- Rendimiento puede verse afectado debido a varias llamadas de dibujo
- No se puede implementar con precisión, algunos efectos visuales, como la transparencia, tal como se analiza más adelante


### <a name="card-draw-calls"></a>Llamadas de dibujo de tarjeta

El código es una simplificación de lo que podría encontrarse en un completo *juego de cartas que se pueden recopilar* (CCG) como "Magia: la recopilación" o "Hearthstone". Nuestro juego sólo muestra tres cartas a la vez y tiene un número pequeño de posibles unidades (azul, verde y naranja). Por el contrario, un juego completo puede tener más de veinte tarjetas en pantalla en un momento dado y reproductores pueden tener cientos de tarjetas para elegir al crear sus cubiertas. Aunque nuestro juego no actualmente sufren problemas de rendimiento, es posible que un juego completo con una implementación similar.

CocosSharp proporciona una idea de rendimiento de la representación mediante la exposición de las llamadas a draw realizadas por fotograma. Nuestro `GameLayer.AddedToScene` método establece el `GameView.Stats.Enabled` a `true`, con lo que se muestra en la parte inferior izquierda de la pantalla de información de rendimiento:

![](ccrendertexture-images/image2.png "El método GameLayer.AddedToScene establece el GameView.Stats.Enabled en true, lo que genera información de rendimiento que se muestra en la parte inferior izquierda de la pantalla")

Observe que a pesar de tener tres tarjetas en pantalla, tenemos diecinueve llamadas a draw (dibujar los resultados de cada tarjeta en seis llamadas, el texto mostrará las cuentas de la información de rendimiento para uno más). Las llamadas a Draw tienen un impacto significativo en el rendimiento de un juego, por lo que CocosSharp proporciona varias maneras para reducirlos. Una técnica se describe en el [CCSpriteSheet guía](~/graphics-games/cocossharp/ccspritesheet.md). Otra técnica consiste en usar el `CCRenderTexture` para reducir cada entidad hasta una llamada, tal como se examinarán en esta guía.


### <a name="card-transparency"></a>Transparencia de la tarjeta

Nuestro `Card` entidad incluye un `Opacity` propiedad con la transparencia de control como se muestra en el siguiente fragmento de código:


```csharp
public override byte Opacity
{
    get
    {
        return base.Opacity;
    }
    set
    {
        base.Opacity = value;
        if (usesRenderTexture)
        {
            this.renderTexture.Sprite.Opacity = value;
        }
        else
        {
            foreach (var component in visualComponents)
            {
                component.Opacity = value;
            }
        }
    }
}
```

Tenga en cuenta que el establecedor admite el uso de representar las texturas o individualmente en cada componente de representación. Para ver su efecto, cambie el `opacity` valor `127` (aproximadamente la mitad opacidad) en `GameLayer.AddedToScene` que dará como resultado de cada componente tiene un `Opacity` valor de `127`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    const byte opacity = 127;
    ...
}
```

El juego ahora representará las tarjetas con algunos transparencia, provocando que aparezcan más oscuros, puesto que es el fondo negro:

![](ccrendertexture-images/image3.png "El juego ahora representará las tarjetas con algunos transparencia, provocando que aparezcan más oscuros, puesto que el fondo es negro")

A primera vista sería como si las tarjetas correctamente se realizaron transparentes. Sin embargo, la captura de pantalla muestra un número de problemas visuales.

Puesto que nuestro en segundo plano es negro, esperaríamos que se convertiría en todas las partes de la tarjeta más oscura debido a la transparencia. Es decir, se convierte en una tarjeta más transparente, más oscuro se convierte en. En la opacidad 0, una `Card` es completamente transparente (completamente negro). Sin embargo, algunas partes de la tarjeta no se vuelva más oscuros cuando la opacidad se cambió a `127`. Aún peor, algunas partes de la tarjeta realmente pasó a ser más brillantes al que se hicieron más trasparentes. Echemos un vistazo a las partes de la tarjeta que estaban negras *antes* fueran transparentes tanto específicamente el texto de detalle y los contornos alrededor del gráfico monster negros. Si se coloca en paralelo, podemos ver el impacto de aplicar la transparencia:

![](ccrendertexture-images/image4.png "Si se coloca en paralelo, se puede ver el impacto de aplicar la transparencia")

Como se mencionó anteriormente, todas las partes de la tarjeta deben se vuelva más oscuras cuando cada vez más transparente, pero en varias áreas esto no es así:

- Esquema del robot se vuelve más claro (pasa del negro al gris)
- El texto de descripción se vuelve más claro (pasa del negro al gris)
- La parte verde del robot se satura menor, pero no se vuelva más oscura

Para ayudar a visualizar el por qué ocurre esto, debemos tener en cuenta que cada componente visual está dibujado por separado, cada uno de ellos parcialmente transparente. El primer componente visual dibujar es el fondo de la tarjeta. Los elementos transparentes subsiguientes se va a dibujar encima de la tarjeta y se verán afectados por el fondo de la tarjeta. Si se quita algún texto desde la tarjeta y baja el gráfico de robot, podemos ver cómo afecta el fondo de la tarjeta al robot. Observe que la línea naranja en el cuadro superior se puede ver en el robot y que el área del robot que se superpone a la banda azul en el centro de la tarjeta se dibuja más oscuros:

![](ccrendertexture-images/image5.png "Observe la línea naranja en el cuadro superior se puede ver en el robot y que el área del robot que se superpone a la banda azul en el centro de la tarjeta se dibuja más oscuro")

Mediante un `CCRenderTexture` nos permite realizar la tarjeta toda transparente sin afectar a la representación de los componentes individuales dentro de la tarjeta, como veremos más adelante en esta guía.


## <a name="using-ccrendertexture"></a>Uso de CCRenderTexture

Ahora que hemos identificado los problemas de representación individualmente cada componente, deberá activar la representación a un `CCRenderTexture` y comparar el comportamiento.

Para habilitar la representación a un `CCRenderTexture`, cambie el `userRenderTextures` variable `true` en `GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>Llamadas de dibujo de tarjeta

Si se ejecuta el juego ahora, veremos las llamadas de dibujo reducidas de diecinueve a cuatro (cada tarjeta se reduce de seis a uno):

![](ccrendertexture-images/image6.png "Si el juego se ejecuta ahora, las llamadas a draw reducción de diecinueve a cuatro cada tarjeta reducido de seis a uno")

Como se mencionó anteriormente, este tipo de reducción puede tener un impacto significativo en juegos con más entidades visuales en pantalla.


### <a name="card-transparency"></a>Transparencia de la tarjeta

Una vez el `useRenderTextures` está establecido en `true`, tarjetas transparentes se representarán de forma diferente:

![](ccrendertexture-images/image7.png "Una vez que el useRenderTextures está establecido en true, transparentes tarjetas se representará de forma diferente")

Vamos a comparar la tarjeta de robot transparente con las texturas de representación (izquierda) frente a sin (derecha):

![](ccrendertexture-images/image8.png "Comparar la tarjeta de robot transparente con vs (izquierdo) de las texturas de representación sin (derecha)")

Las diferencias más obvias se encuentran en el texto de detalles (negro en lugar de color gris claro) y el sprite robot (oscuro en lugar de luz y sin saturación).


## <a name="ccrendertexture-details"></a>Detalles de CCRenderTexture

Ahora que hemos visto las ventajas de usar `CCRenderTexture`, echemos un vistazo a cómo se utiliza en el `Card` entidad.

El `CCRenderTexture` es un lienzo que puede ser el destino de representación. Tiene dos diferencias principales en comparación con la pantalla de juego:

1. El `CCRenderTexture` persiste entre marcos. Esto significa que un `CCRenderTexture` es necesario representar solo cuando se producen cambios. En nuestro caso, el `Card` entidad nunca cambia, por lo que se procesa solo una vez. Si cualquier `Card` componentes cambiado y, después, la tarjeta deberá actualizarse a sí mismo a su `CCRenderTexture`. Por ejemplo, si se cambia el valor de HP (puntos de estado) cuando es atacado, la tarjeta necesitaría para representarse a sí misma para reflejar el nuevo valor de HP.
1. El `CCRenderTexture` dimensiones de píxeles no están vinculadas a la pantalla. Un `CCRenderTexture` puede ser mayor o menor que la resolución del dispositivo. El `Card` código crea un `CCRenderTexture` según el tamaño de su sprite en segundo plano. La tarjeta contiene una referencia a un `CCRenderTexture` llamado `renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

El `renderTexture` instancia permanece `null` hasta que el `UseRenderTexture` se asigna una propiedad en true, que llama a `SwitchToRenderTexture`:


```csharp
private void SwitchToRenderTexture()
{
    // The card needs to be moved to the origin (0,0) so it's rendered on the render target. 
    // After it's rendered to the CCRenderTexture, it will be moved back to its old position
    var oldPosition = this.Position;
    // Make sure visuals are part of the card so they get rendered
    bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
    if (!areVisualComponentsAlreadyAdded)
    {
        // Temporarily add them so we can render the object:
        foreach (var component in visualComponents)
        {
            this.AddChild(component);
        }
    }
    // Create the render texture if it hasn't yet been made:
    if (renderTexture == null)
    {
        // Even though the game is zoomed in to create a pixellated look, we are using
        // high-resolution textures. Therefore, we want to have our canvas be 2x as big as 
        // the background so fonts don't appear pixellated
        var pixelResolution = background.ContentSize * 2;
        var unitResolution = background.ContentSize;
        renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
        //renderTexture.Sprite.Scale = .5f;
    }
    // We don't want the render target to be a child of this when we update it:
    if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
    {
        this.Children.Remove(renderTexture.Sprite);
    }
    // Move this instance back to the origin so it is rendered inside the render texture:
    this.Position = CCPoint.Zero;
    // Clears the CCRenderTexture
    renderTexture.BeginWithClear(CCColor4B.Transparent);
    // Visit renders this object and all of its children
    this.Visit();
    // Ends the rendering, which means the CCRenderTexture's Sprite can be used
    renderTexture.End();
    // We no longer want the individual components to be drawn, so remove them:
    foreach (var component in visualComponents)
    {
        this.RemoveChild(component);
    }
    // Move this back to its original position:
    this.Position = oldPosition;
    // add the render texture sprite to this:
    renderTexture.Sprite.AnchorPoint = CCPoint.Zero;
    this.AddChild(renderTexture.Sprite);
}
```

El `SwitchToRenderTexture` puede llamarse al método cada vez que la textura debe actualizarse. Puede llamarse si la tarjeta ya está usando su `CCRenderTexture` o que cambia el `CCRenderTexture` por primera vez.

Las secciones siguientes exploran el `SwitchToRenderTexture` método. 


### <a name="ccrendertexture-size"></a>Tamaño CCRenderTexture

El constructor CCRenderTexture requiere dos conjuntos de dimensiones. La primera controla el tamaño de la `CCRenderTexture` cuando se dibuja y el segundo especifica el ancho en píxeles y el alto de su contenido. El `Card` crea una instancia de entidad su `CCRenderTexture` con el fondo [ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/). Nuestro juego tiene un `DesignResolution` de 512 por 384, como se muestra en `ViewController.LoadGame` en iOS y `MainActivity.LoadGame` en Android:


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

El `CCRenderTexture` se llama al constructor con la `background.ContentSize` como primer parámetro, que indica que el `CCRenderTexture` debe ser tan importante como el fondo `CCSprite`. Desde el fondo de la tarjeta `CCSprite` es 200 píxeles de alto, la tarjeta ocupará aproximadamente la mitad de la altura vertical de la pantalla.

El segundo parámetro pasado a la `CCRenderTexture` constructor especifica la resolución de píxeles de la `CCRenderTexture`. Como se describe en el [Guía de resolución de CocosSharp](~/graphics-games/cocossharp/resolutions.md), el ancho y alto del área visible en unidades de juegos a menudo no es igual que la resolución de píxeles de la pantalla. De forma similar, un CCRenderTexture podría utilizar una resolución mayor que su tamaño para que los objetos visuales aparezcan más nítidos en dispositivos de alta resolución.

La resolución de píxel es dos veces el tamaño de la CCRenderTexture para evitar que el texto de pixelado atractivo:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

Para comparar, podemos cambiar el valor de pixelResolution para que coincida con el `background.ContentSize` (sin duplicaba) y comparar el resultado: 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "Para comparar, puede cambiar el valor de pixelResolution para que coincida con el fondo. ContentSize sin que se va a duplicar y comparar el resultado")


### <a name="rendering-to-a-ccrendertexture"></a>Representar en un CCRenderTexture

Normalmente, los objetos visuales en CocosSharp no se representan explícitamente. En su lugar, se agregan los objetos visuales a un `CCLayer` que forma parte de un `CCScene`. CocosSharp representa de forma automática el `CCScene` y en su jerarquía visual en cada fotograma sin ningún código de representación que se llama. 

Por el contrario, el `CCRenderTexture` debe dibujarse a explícitamente. Esta representación puede dividirse en tres pasos:

1. `CCRenderTexture.BeginWithClear` se llama, que indica que todas las presentaciones posteriores tendrán como destino la llamada a `CCRenderTexture`.
1. Heredar de los objetos `CCNode` (como el `Card` entidad) se representan en el `CCRenderTexture` mediante una llamada a `Visit`.
1. `CCRenderTexture.End` se llama, que indica que la representación a la `CCRenderTexture` completada.

Se puede representar cualquier número de objetos en un `CCRenderTexture` entre su `Begin` y `End` llamadas. Antes de la representación, todos los objetos visibles necesarios se agregan como elementos secundarios:


```csharp
bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
if (!areVisualComponentsAlreadyAdded)
{
    // Temporarily add them so we can render the object:
    foreach (var component in visualComponents)
    {
        this.AddChild(component);
    }
}
```

El `renderTexture` no deben formar parte de la tarjeta al representar, por lo que se quita:


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

Ahora el `Card` instancia puede representarse a sí misma a la `CCRenderTexture` instancia:


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

Una vez finalizado el procesamiento, se quitan los componentes individuales y el `CCRenderTexture` se vuelven a agregar.


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

## <a name="summary"></a>Resumen

Esta guía se trata la `CCRenderTexture` clase mediante el uso de un `Card` entidad que se pueden usar en un juego de cartas recopilable. Se ha mostrado cómo usar el `CCRenderTexture` clase para mejorar la velocidad de fotogramas e implementar correctamente la transparencia de toda la entidad.

Aunque esta guía usa un `CCRenderTexture` dentro de una entidad, puede ser esta clase se usa para representar varias entidades o incluso todo `CCLayer` instancias para los efectos de toda la pantalla y mejoras de rendimiento.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de la API CCRenderTexture](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [Proyecto completo (ejemplo)](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
