---
title: Animación con CCAction
description: La clase CCAction simplifica agregar animaciones a los juegos de CocosSharp. Estas animaciones pueden usarse para implementar la funcionalidad o a pulir.
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: c486bb2e78579360e0f935219cd82958fedee34b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357730"
---
# <a name="animating-with-ccaction"></a>Animación con CCAction

_La clase CCAction simplifica agregar animaciones a los juegos de CocosSharp. Estas animaciones pueden usarse para implementar la funcionalidad o a pulir._

`CCAction` es una clase base que se puede usar para animar objetos CocosSharp. Esta guía explica integrado `CCAction` implementaciones para tareas comunes, como la posición, la escala y rotación. También examina cómo crear implementaciones personalizadas heredando de `CCAction`.

Esta guía usa un proyecto denominado **ActionProject** que [puede descargarse aquí](https://developer.xamarin.com/samples/mobile/CCAction). Esta guía se usa el `CCDrawNode` (clase), que se trata en el [dibujar geometría con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md) guía.


## <a name="running-the-actionproject"></a>Ejecuta el ActionProject

**ActionProject** es una solución de CocosSharp que se puede compilar para iOS y Android. Actúa como código de ejemplo de cómo usar el `CCAction` clase y como una demostración en tiempo real de common `CCAction` implementaciones.

Cuando se ejecuta, ActionProject muestra tres `CCLabel` instancias de la izquierda de la pantalla y un objeto visual que se dibujan en dos `CCDrawNode` instancias para ver las distintas acciones:

![](ccaction-images/image1.png "ActionProject muestra tres instancias CCLabel a la izquierda de la pantalla y un objeto visual que dibuja dos instancias de CCDrawNode para ver las distintas acciones")

Las etiquetas de la izquierda indican qué tipo de `CCAction` se creará al puntear en la pantalla. De forma predeterminada, el **posición** valor está seleccionado, lo que resulta en un `CCMove` acción que se crearon y aplicaron en el círculo rojo:

![](ccaction-images/image2.gif "El valor de posición está activado, lo que una acción de CCMove se crearon y aplicaron en el círculo rojo")

Al hacer clic en las etiquetas de la izquierda cambia el tipo de `CCAction` se realiza en el círculo. Por ejemplo, al hacer clic en el **posición** etiqueta recorrerá los distintos valores que se pueden cambiar:

![](ccaction-images/image3.gif "Al hacer clic en la etiqueta de posición recorrerá los distintos valores que se pueden cambiar")

## <a name="common-variable-changing-ccaction-classes"></a>Clases comunes de CCAction cambiar a la variable

El **ActionProject** usa las siguientes `CCAction`-herencia de clases, que forman parte de CocosSharp:

 - `CCMoveTo` : Cambia una `CCNode` la instancia `Position` propiedad
 - `CCScaleTo` : Cambia una `CCNode` la instancia `Scale` propiedad
 - `CCRotateTo` : Cambia una `CCNode` la instancia `Rotation` propiedad

Esta guía hace referencia a estas acciones como *cambiar a la variable*, lo que significa que afecten directamente a la variable de la `CCNode` que se agregan a. Otros tipos de acciones se denominan *aceleración* acciones, que se tratan más adelante en esta guía.

El **ActionProject** demuestra que es el propósito de estas acciones modificar una variable con el tiempo. En concreto, estos `CCActions` constructores toman dos argumentos: longitud de tiempo para realizar y el valor que se asigna. El siguiente fragmento de código muestra cómo se crean los tres tipos de acciones:


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

Una vez creada la acción, se agrega a un CCNode como sigue:


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` se inicia la `CCAction` comportamiento de la instancia y se realizará automáticamente su lógica después de fotograma hasta la finalización.

Cada uno de los tipos enumerados anteriormente finaliza con la palabra *a* lo que significa que el `CCAction` modificará el `CCNode` para que el valor del argumento representa el estado final cuando ha finalizado la acción. Por ejemplo, creando un `CCMoveTo` con una posición de X = 100 e Y = 200 produce el `CCNode` la instancia `Position` está establecido en X = 100, Y = 200 al final de la hora especificada, independientemente de la `CCNode` ubicación inicial de la instancia.

Cada uno "A" de la clase también tiene una versión "Por", que se agregará el valor del argumento en el valor actual en el `CCNode`. Por ejemplo, creando un `CCMoveBy` con una posición de X = 100 e Y = 200 dará como resultado la `CCNode` instancia que se mueven a las unidades de la derecha 100 y 200 unidades de la posición en que estaba cuando se inició la acción.


## <a name="easing-actions"></a>Acciones de entradas y salidas lentas

De forma predeterminada, llevará a cabo las acciones de cambio a la variable *interpolación lineal* : la acción se moverá hacia el valor deseado a una velocidad constante. Si interpolar *posición* linealmente, el objeto inmediatamente iniciará y detendrá mover al principio y al final de la acción, y su velocidad permanecerá constante mientras se ejecuta la acción. 

Interpolación lineal no es menos discordante y agrega un elemento de polaco, por lo que CocosSharp ofrece una variedad de acciones que pueden usarse para modificar las acciones de cambio a la variable de aceleración.

En el **ActionProject** ejemplo, podemos cambiar entre estos tipos de entradas y salidas lentas acciones, haga clic en la segunda etiqueta (cuyo valor predeterminado es **<None>**):

![](ccaction-images/image4.gif "El usuario puede alternar entre estos tipos de entradas y salidas lentas acciones, haga clic en la segunda etiqueta")

Acciones de entradas y salidas lentas son especialmente eficaces porque no están vinculados a una acción de configuración de variable determinada. Esto significa que la misma acción de entradas y salidas lenta puede usarse para asignar la posición, rotación, escala o acciones personalizadas (como veremos más adelante en esta guía).

Acciones de entradas y salidas lentas ajustan acciones de configuración de variable (siempre y cuando la acción de configuración de la variable que se hereda de `CCFiniteTimeAction`) mediante la aceptación de una acción de configuración de la variable como argumento en sus constructores.

Por ejemplo, si las etiquetas se establecen en **posición**, **CCEaseElastic**, a continuación, el siguiente código se ejecutará cuando se detecta una entrada táctil (tenga en cuenta que el código se ha omitido para resaltar las líneas correspondientes):


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

Como se muestra en la aplicación, la aceleración exacta mismo puede aplicarse a otras acciones de configuración de la variable como `CCRotateTo`:

![](ccaction-images/image5.gif "La aceleración exacta mismo puede aplicarse a otras acciones de configuración de la variable como CCRotateTo")


## <a name="easing-in-out-and-inout"></a>En, Out e InOut de aceleración

Todas las acciones de entradas y salidas lentas tienen `In`, `Out`, o `InOut` anexado al tipo de entradas y salidas lenta. Estos términos hacen referencia a cuando se aplica la aceleración: `In` de aceleración desea aplicar al principio, `Out` significa que al final, y `InOut` significa tanto al principio y al final.

Un `In` aceleración acción afecta a la manera en que una variable se aplica a lo largo de la interpolación toda (tanto al principio y al final), pero normalmente las características más reconocibles de la acción de entradas y salidas lenta llevará a cabo al principio. De forma similar, `Out` entradas y salidas de acciones se caracterizan por su comportamiento al final de la interpolación. Por ejemplo, `CCEaseBounceOut` dará como resultado un objeto rebote al final de la acción.


### <a name="out"></a>Out

`Out` por lo general de aceleración aplica los cambios más notorios al final de la interpolación. Por ejemplo, `CCEaseExponentialOut` ralentizará la tasa de cambio de la variable cambiante medida que se acerque el valor de destino:

![](ccaction-images/image6.gif "CCEaseExponentialOut ralentizará la tasa de cambio de la variable de cambio a medida que se acerque el valor de destino")


### <a name="in"></a>En

`In` por lo general de aceleración aplica el cambio más notable al principio de la interpolación. Por ejemplo, `CCEaseExponentialIn` moverá más lentamente al principio de la acción:

![](ccaction-images/image7.gif "CCEaseExponentialIn moverá más lentamente al principio de la acción")


### <a name="inout"></a>InOut

`InOut` Generalmente, aplica los cambios más notorios tanto al principio y al final. `InOut` la aceleración es normalmente simétrica. Por ejemplo, `CCEaseExponentialInOut` moverá lentamente al principio y al final de la acción:

![](ccaction-images/image8.gif "CCEaseExponentialInOut moverá lentamente al principio y al final de la acción")


## <a name="implementing-a-custom-ccaction"></a>Implementar un CCAction personalizado

Todas las clases que hemos explicado hasta ahora se incluyen en CocosSharp para proporcionar funcionalidad común. Custom `CCAction` implementaciones pueden proporcionar flexibilidad adicional. Por ejemplo, un `CCAction` que controla la relación de rellenada de una barra de la experiencia puede usarse para que la barra de la experiencia sin problemas aumenta cada vez que el usuario gana experiencia.

`CCAction` Normalmente, las implementaciones requieren dos clases:

 - `CCFiniteTimeAction` implementación de la clase de acción de tiempo finito es responsable de iniciar la acción. Es la clase que se crea una instancia y bien agregarse directamente a un `CCNode` o a una acción de entradas y salidas lenta. Debe crear una instancia y devolver un `CCFiniteTimeActionState`, que llevará a cabo las actualizaciones.
 - `CCFiniteTimeActionState` implementación de la clase de estado de acción de tiempo finito es responsable de actualizar las variables implicadas en la acción. Debe implementar una función de actualización, que asigna el valor en el destino de acuerdo con un valor de hora. Esta clase no es hace referencia explícita a fuera de la `CCFiniteTimeAction` que lo crea. Simplemente funciona "en segundo plano".

**ActionProject** proporciona un personalizado `CCFiniteTimeAction` implementación denominada `LineWidthAction,` que se usa para ajustar el ancho de la línea amarilla dibujado en la parte superior del círculo rojo. Tenga en cuenta que su trabajo solo es crear instancias y devolver un `LineWidthState` instancia:


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

Como se mencionó anteriormente, el `LineWidthState` realiza el trabajo de la asignación de la línea `Width` propiedad según cuánto `time` ha pasado:


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

El LineWidthAction se puede combinar con ninguna acción de entradas y salidas lenta para cambiar el ancho de línea de varias maneras, como se muestra en la siguiente animación:

![](ccaction-images/image9.gif "El LineWidthAction se puede combinar con ninguna acción de entradas y salidas lenta para cambiar el ancho de línea de varias maneras, como se muestra en esta animación")


### <a name="interpolation-and-the-update-method"></a>La interpolación y el método de actualización

La lógica única, además de almacenar valores en las clases anteriores, se encuentra en la `LineWidthState.Update` método. El `startWidth` variable almacena el ancho del destino `LineNode` al principio de la acción y el `deltaWidth` variable almacena el valor de cuánto cambiará durante el transcurso de la acción.

Sustituyendo el `time` variable con un valor de 0, podemos ver que el destino `LineNode` estará en su posición inicial:


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

Del mismo modo, podemos ver que el destino `LineNode` estará a su destino mediante la sustitución de la variable de tiempo con un valor de 1:


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

El `time` valor suele ser entre 0 y 1 - aunque no siempre - y `Update` implementaciones no deben suponer estos límites. Algunos métodos de entradas y salidas lentas (como `CCEaseBackIn` y `CCEaseBackOut`) proporcionará un valor de hora fuera del intervalo de 0 a 1.


## <a name="conclusion"></a>Conclusión

Interpolación y aceleración son una parte fundamental de la creación de un juego precisos, especialmente al crear interfaces de usuario. Esta guía explica cómo usar `CCActions` para interpolar valores estándar como la posición y rotación, así como los valores personalizados. El `LineWidthState` y `LineWidthAction` clases muestran cómo implementar una acción personalizada.

## <a name="related-links"></a>Vínculos relacionados

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [Ejemplo completo](https://developer.xamarin.com/samples/mobile/CCAction/)
