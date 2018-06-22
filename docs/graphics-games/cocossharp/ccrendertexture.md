---
title: Rendimiento y efectos visuales con CCRenderTexture
description: CCRenderTexture permite a los desarrolladores a mejorar el rendimiento de sus juegos, CocosSharp al reducir las llamadas a draw y puede utilizarse para crear efectos visuales. Esta guía incluye en el ejemplo CCRenderTexture para proporcionar un ejemplo práctico de cómo utilizar esta clase de forma eficaz.
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 82d39542b24c6b1669798a0b4e1fb14a81f6b44e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922527"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>Rendimiento y efectos visuales con CCRenderTexture

_CCRenderTexture permite a los desarrolladores a mejorar el rendimiento de sus juegos, CocosSharp al reducir las llamadas a draw y puede utilizarse para crear efectos visuales. Esta guía incluye en el ejemplo CCRenderTexture para proporcionar un ejemplo práctico de cómo utilizar esta clase de forma eficaz._

La `CCRenderTexture` clase proporciona funcionalidad para representar varios objetos de CocosSharp a una textura única. Una vez creada, `CCRenderTexture` instancias pueden usarse para representar gráficos de forma eficaz y para implementar los efectos visuales. `CCRenderTexture` permite que varios objetos que se representará en una textura solo una vez. A continuación, esa textura puede ser volver a usar todos los fotogramas, lo que reduce el número total de llamadas a draw.

Esta guía examina cómo usar la `CCRenderTexture` objeto para mejorar el rendimiento de las tarjetas de presentación en un juego de cartas recopilable (CCG). También se muestra cómo `CCRenderTexture` puede utilizarse para hacer que una entidad completa transparente. Esta guía hace referencia el `CCRenderTexture` [proyecto de ejemplo](https://developer.xamarin.com/samples/mobile/CCRenderTexture/).

![](ccrendertexture-images/image1.png "Esta guía hace referencia al proyecto de ejemplo de CCRenderTexture")


## <a name="card--a-typical-entity"></a>Tarjeta: una entidad típica

Antes de examinar cómo usar `CCRenderTexture` objeto, primero deberá familiarizarse nosotros mismos con el `Card` entidad que vamos a usar a lo largo de este proyecto para explorar el `CCRenderTexture` clase. El `Card` clase es una entidad típica, siguiendo el modelo de entidad que se describen en la [Guía de la entidad](~/graphics-games/cocossharp/entities.md). La clase de tarjeta tiene todos sus componentes visuales (instancias de `CCSprite` y `CCLabel`) aparecen como campos:


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

Instancias de tarjeta se pueden representar mediante el uso de un `CCRenderTexture`, o mediante el dibujo de cada componente visual individualmente. Aunque cada componente es un objeto independiente, el `CCNode` relaciones jerárquicas de sistema que se usa en entidades hace el `Card` se comportan como un único objeto, al menos en su mayor parte. Por ejemplo, si un `Card` mover, cambia de tamaño o girar entidad, a continuación, se ven afectados todos los objetos visuales independientes para que la tarjeta aparezca como un único objeto. Para ver las tarjetas se comportan como un único objeto, se podrá modificar el `GameLayer.AddedToScene` método para establecer el `useRenderTextures` variable `false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

El `GameLayer` código no mueve cada elemento visual por separado, aún cada elemento visual en el `Card` entidad se coloca correctamente:

![](ccrendertexture-images/image1.png "El código GameLayer no mueve cada elemento visual por separado, pero cada elemento visual dentro de la entidad de la tarjeta se coloca correctamente")

El ejemplo está codificado para exponer dos problemas que pueden producirse cuando cada componente visual se representa a sí misma:

- Rendimiento puede verse afectado debido a varias llamadas de dibujo
- Algunos efectos visuales, como la transparencia, no se puede implementar con precisión, exploraremos más adelante


### <a name="card-draw-calls"></a>Llamadas de dibujo de tarjeta

El código es una simplificación de lo que se pueden encontrar en un completo *juego de cartas recopilable* (CCG) como "Magic: la recopilación" o "Hearthstone". Nuestro juego solo muestra tres cartas a la vez y tiene un número pequeño de posibles unidades (azul, verde y naranja). Por el contrario, un juego completo puede tener más de veinte tarjetas que aparecen en pantalla en un momento dado y reproductores pueden tener cientos de tarjetas que elegir al crear sus barajas. Aunque el juego no sufren actualmente de problemas de rendimiento, podría un juego completo con implementación similar.

CocosSharp proporciona información sobre el rendimiento de la representación mediante la exposición de las llamadas a draw realiza por fotograma. Nuestro `GameLayer.AddedToScene` método establece la `GameView.Stats.Enabled` a `true`, da lugar a que se muestra en la parte inferior izquierda de la pantalla de información de rendimiento:

![](ccrendertexture-images/image2.png "El método GameLayer.AddedToScene establece el GameView.Stats.Enabled en true, lo que se muestra en la parte inferior izquierda de la pantalla de información de rendimiento")

Tenga en cuenta que a pesar de tener tres tarjetas en pantalla, tenemos las llamadas a draw años (dibuja cada tarjeta da resultado seis llamadas, el texto que muestra las cuentas de la información de rendimiento de una o más). Las llamadas a Draw tienen un impacto significativo en el rendimiento de un juego, por lo que CocosSharp proporciona varias maneras de reducir su. Una técnica se describe en el [CCSpriteSheet guía](~/graphics-games/cocossharp/ccspritesheet.md). Otra técnica consiste en utilizar el `CCRenderTexture` para reducir cada entidad hasta una llamada, tal y como examinaremos en esta guía.


### <a name="card-transparency"></a>Transparencia de tarjeta

Nuestro `Card` entidad incluye un `Opacity` propiedad transparencia de control como se muestra en el siguiente fragmento de código:


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

Observe que el establecedor admite el uso de representar las texturas o en la representación de cada componente individualmente. Para ver su efecto, cambie la `opacity` valor `127` (aproximadamente la mitad opacidad) en `GameLayer.AddedToScene` que dará como resultado en cada componente que tiene un `Opacity` valo `127`:


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

El juego ahora representará las tarjetas con algunos transparencia, provocando que aparezcan más oscuros, puesto que el fondo es negro:

![](ccrendertexture-images/image3.png "El juego ahora representará las tarjetas con algunos transparencia, provocando que aparezcan más oscuros, puesto que el fondo es negro")

A primera vista sería como si las tarjetas correctamente se realizaron transparentes. Sin embargo, la captura de pantalla muestra una serie de problemas visuales.

Puesto que el fondo es negro, se podría esperar que todas las partes de la tarjeta se convertiría en cuanto más oscuro sea debido a la transparencia. Es decir, se convierte en una tarjeta más transparente, más oscuro se convierte en. En opacidad 0, un `Card` es completamente transparente (totalmente negro). Sin embargo, algunas partes de la tarjeta no están más oscuros cuando opacidad cambió a `127`. Peor aún, algunas partes de la tarjeta realmente pasara a ser más brillantes cuando pasara a ser más transparentes. Echemos un vistazo a partes de la tarjeta que fueran negros *antes de* fueran transparentes – específicamente el texto de detalle y los contornos negros alrededor del gráfico de monster. Si se coloca estos en paralelo, podemos ver el impacto de aplicar la transparencia:

![](ccrendertexture-images/image4.png "Si se coloca en paralelo, se puede ver el impacto de aplicar la transparencia")

Como se mencionó anteriormente, todas las partes de la tarjeta deben convertirse en más oscuras cuando pase a ser más transparente, pero en varios aspectos que esto no es así:

- Esquema del robot se vuelve más claro (sale de negro en gris)
- El texto de descripción se vuelve más claro (sale de negro en gris)
- La parte verde del robot se satura menor, pero no se cargue más oscura

Para ayudar a visualizar por qué ocurre esto, es necesario tener en cuenta que cada componente visual está dibujado por separado, cada parte transparente. El primer componente visual que dibuja es el fondo de la tarjeta. Elementos transparentes subsiguientes se dibujará encima de la tarjeta y se verán afectados por el fondo de la tarjeta. Si se elimina parte del texto de la tarjeta y baja el gráfico de robot, podemos ver cómo afecta el fondo de la tarjeta al robot. Observe la línea naranja en el cuadro superior se pueden ver en el robot, y que el área del robot que se superpone a la banda azul en el centro de la tarjeta se dibuja más oscuros:

![](ccrendertexture-images/image5.png "Observe la línea naranja en el cuadro superior se pueden ver en el robot, y que el área del robot que se superpone a la banda azul en el centro de la tarjeta se dibuja más oscuro")

Mediante un `CCRenderTexture` nos permite que la tarjeta toda transparente sin afectar a la representación de los componentes individuales dentro de la tarjeta, tal y como se verá más adelante en esta guía.


## <a name="using-ccrendertexture"></a>Usar CCRenderTexture

Ahora que hemos identificado los problemas de representación individualmente cada componente, podrá activar representación para un `CCRenderTexture` y comparar el comportamiento.

Para habilitar la representación para un `CCRenderTexture`, cambie la `userRenderTextures` variable `true` en `GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>Llamadas de dibujo de tarjeta

Si se ejecuta el juego ahora, veremos las llamadas a draw reducidas de diecinueve a cuatro (cada tarjeta reduce entre seis a uno):

![](ccrendertexture-images/image6.png "Si el juego se ejecuta ahora, las llamadas a draw reducción de diecinueve a cuatro cada tarjeta reduce de seis a uno")

Como se mencionó anteriormente, este tipo de reducción puede tener un impacto significativo en juegos con más entidades visuales en pantalla.


### <a name="card-transparency"></a>Transparencia de tarjeta

Una vez el `useRenderTextures` se establece en `true`, tarjetas transparentes se representarán de forma diferente:

![](ccrendertexture-images/image7.png "Una vez que la useRenderTextures se establece en true, transparentes tarjetas se representará de forma diferente")

Vamos a comparar la tarjeta de robot transparente utilizando texturas de representación (izquierda) frente a sin (derecha):

![](ccrendertexture-images/image8.png "Comparar la tarjeta de robot transparente con vs (izquierdo) de representación texturas sin (derecha)")

Las diferencias más obvias se encuentran en el texto de detalles (negro en lugar de color gris claro) y el sprite robot (oscuro en lugar de luz y sin saturación).


## <a name="ccrendertexture-details"></a>Detalles de CCRenderTexture

Ahora que hemos visto las ventajas de usar `CCRenderTexture`, echemos un vistazo a cómo se utiliza en el `Card` entidad.

El `CCRenderTexture` es un lienzo que puede ser el destino de representación. Tiene dos diferencias principales en comparación con la pantalla del juego:

1. El `CCRenderTexture` persiste entre fotogramas. Esto significa que un `CCRenderTexture` es necesario representar solo cuando se producen cambios. En nuestro caso, el `Card` entidad nunca cambia, por lo que solo se representa una vez. Si cualquier `Card` componentes cambiado, a continuación, la tarjeta tendría que volver a dibujar automáticamente para su `CCRenderTexture`. Por ejemplo, si se cambia el valor de HP (puntos de estado) cuando un ataque, la tarjeta necesitaría para representarse a sí misma para reflejar el nuevo valor de HP.
1. El `CCRenderTexture` dimensiones de píxeles no están asociados a la pantalla. Un `CCRenderTexture` puede ser mayor o menor que la resolución del dispositivo. El `Card` código crea un `CCRenderTexture` según el tamaño de su sprite de fondo. La tarjeta contiene una referencia a un `CCRenderTexture` denominado `renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

El `renderTexture` instancia sigue siendo `null` hasta que el `UseRenderTexture` se asigna la propiedad en true, que llama `SwitchToRenderTexture`:


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

La `SwitchToRenderTexture` puede llamarse al método cada vez que la textura tiene que actualizarse. Se puede llamar a si la tarjeta ya está usando su `CCRenderTexture` o cambie a la `CCRenderTexture` por primera vez.

En las siguientes secciones explorar el `SwitchToRenderTexture` método. 


### <a name="ccrendertexture-size"></a>Tamaño de CCRenderTexture

El constructor CCRenderTexture requiere dos conjuntos de dimensiones. La primera controla el tamaño de la `CCRenderTexture` cuando se dibuja, y el segundo especifica el ancho de píxel y el alto de su contenido. El `Card` crea una instancia de entidad su `CCRenderTexture` con el fondo [tamaño de contenido](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/). Nuestro juego tiene un `DesignResolution` de 512 por 384, tal y como se muestra en `ViewController.LoadGame` en iOS y `MainActivity.LoadGame` en Android:


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

El `CCRenderTexture` se denomina constructor con la `background.ContentSize` como el primer parámetro, lo que indica que la `CCRenderTexture` debe ser tan importante como el fondo `CCSprite`. Desde el fondo de la tarjeta `CCSprite` es 200 píxeles de alto, la tarjeta ocupará aproximadamente la mitad de la altura vertical de la pantalla.

El segundo parámetro pasado a la `CCRenderTexture` constructor especifica la resolución de píxeles de la `CCRenderTexture`. Como se describe en el [Guía de resolución de CocosSharp](~/graphics-games/cocossharp/resolutions.md), el ancho y alto del área visible en unidades de juegos a menudo no es igual que la resolución de píxeles de la pantalla. De forma similar, un CCRenderTexture podría utilizar una resolución mayor que su tamaño para que objetos visuales aparecen más nítidos en dispositivos de alta resolución.

La resolución de píxel es dos veces el tamaño de la CCRenderTexture para evitar que el texto de la claridad pixelado:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

Para comparar, podemos cambiar el valor de pixelResolution para que coincida con el `background.ContentSize` (sin que se duplicó) y comparar el resultado: 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "Para comparar, puede cambiar el valor de pixelResolution para que coincida con el fondo. ContentSize sin que se va a duplicar y comparar el resultado")


### <a name="rendering-to-a-ccrendertexture"></a>Representación de un CCRenderTexture

Normalmente, los objetos visuales en CocosSharp no se representan explícitamente. En su lugar, se agregan los objetos visuales a un `CCLayer` que forma parte de un `CCScene`. CocosSharp representa automáticamente el `CCScene` y su jerarquía visual en cada marco sin ningún código de representación que se va a llamar. 

Por el contrario, el `CCRenderTexture` debe dibujarse explícitamente a. Esta representación puede dividirse en tres pasos:

1. `CCRenderTexture.BeginWithClear` se llama, que indica que todas las presentaciones posteriores tendrán como destino la llamada a `CCRenderTexture`.
1. Objetos heredar `CCNode` (como el `Card` entidad) se representan en el `CCRenderTexture` mediante una llamada a `Visit`.
1. `CCRenderTexture.End` se llama, que indica que la representación a la `CCRenderTexture` completada.

Se puede representar cualquier número de objetos en un `CCRenderTexture` entre su `Begin` y `End` llamadas. Antes de su representación, todos los objetos visibles necesarios se agregan como elementos secundarios:


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

El `renderTexture` no debe formar parte de la tarjeta al representar, por lo que se quita:


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

Una vez finalizada la representación, se quitan los componentes individuales y `CCRenderTexture` se vuelve a agregar.


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

Esta guía se trata la `CCRenderTexture` clase mediante el uso de un `Card` entidad que se pueda usar en un juego de cartas recopilable. Ha mostrado cómo usar el `CCRenderTexture` clase para mejorar la velocidad de fotogramas e implementar correctamente la transparencia de toda la entidad.

Aunque esta guía usa un `CCRenderTexture` dentro de una entidad, esta clase puede ser usado para representar varias entidades o incluso todo `CCLayer` instancias efectos de toda la pantalla y mejoras de rendimiento.

## <a name="related-links"></a>Vínculos relacionados

- [Referencia de la API de CCRenderTexture](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [Proyecto completo (ejemplo)](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
