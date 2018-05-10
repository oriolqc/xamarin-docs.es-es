---
title: Animar con CCAction
description: La clase CCAction simplifica agregar animaciones a los juegos de CocosSharp. Estas animaciones pueden usarse para implementar la funcionalidad o agregar polaco.
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: b6209816f741423f40945a0fe4391fe921cb35de
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="animating-with-ccaction"></a>Animar con CCAction

_La clase CCAction simplifica agregar animaciones a los juegos de CocosSharp. Estas animaciones pueden usarse para implementar la funcionalidad o agregar polaco._

`CCAction` es una clase base que se puede usar para animar objetos CocosSharp. Esta guía explica integrada `CCAction` implementaciones para las tareas comunes, como la posición, la escala y la rotación. También examina cómo crear las implementaciones personalizadas heredando de `CCAction`.

Esta guía usa un proyecto denominado **ActionProject** que [puede descargarse aquí](https://developer.xamarin.com/samples/mobile/CCAction). Esta guía se usan el `CCDrawNode` (clase), que se explica en la [geometría de dibujo con CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md) guía.


## <a name="running-the-actionproject"></a>Ejecuta el ActionProject

**ActionProject** es una solución de CocosSharp que se puede compilar para iOS y Android. Sirve como un ejemplo de código sobre cómo usar el `CCAction` clase y como una demostración en tiempo real comunes de `CCAction` las implementaciones.

Cuando se ejecuta, ActionProject muestra tres `CCLabel` instancias de la izquierda de la pantalla y un objeto visual que se dibujan dos `CCDrawNode` instancias para ver las distintas acciones:

![](ccaction-images/image1.png "ActionProject muestra tres instancias de CCLabel a la izquierda de la pantalla y un objeto visual que se dibujan en dos instancias de CCDrawNode para ver las distintas acciones")

Las etiquetas de la izquierda indican qué tipo de `CCAction` se creará al puntear en la pantalla. De forma predeterminada, el **posición** valor está seleccionado, lo que produce una `CCMove` acción que se crea y se aplica a un círculo rojo:

![](ccaction-images/image2.gif "El valor de posición está seleccionado, resultante en una CCMove acción que se va a crear y aplicar a un círculo rojo")

Al hacer clic en las etiquetas de la izquierda cambia el tipo de `CCAction` se realiza en el círculo. Por ejemplo, al hacer clic en el **posición** etiqueta recorrerá los distintos valores que se pueden cambiar:

![](ccaction-images/image3.gif "Haga clic en la etiqueta de posición recorrerá los distintos valores que se pueden cambiar")

## <a name="common-variable-changing-ccaction-classes"></a>Clases de CCAction de cambio de variable comunes

El **ActionProject** usa las siguientes `CCAction`-herencia de clases, que forman parte de CocosSharp:

 - `CCMoveTo` : Cambia un `CCNode` la instancia `Position` propiedad
 - `CCScaleTo` : Cambia un `CCNode` la instancia `Scale` propiedad
 - `CCRotateTo` : Cambia un `CCNode` la instancia `Rotation` propiedad

Esta guía hace referencia a estas acciones como *cambiar variable*, lo que significa que afecta directamente la variable de la `CCNode` que se agregan a. Otros tipos de acciones se conocen como *aceleración* acciones, que se tratan más adelante en esta guía.

El **ActionProject** se muestra que es la finalidad de estas acciones modificar una variable con el tiempo. En concreto, estas `CCActions` constructores toman dos argumentos: tiempo de realizar y el valor que se asigna. El siguiente fragmento de código muestra cómo se crean los tres tipos de acciones:


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

Una vez que se crea la acción, se agrega a un CCNode como se indica a continuación:


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` inicia la `CCAction` comportamiento de la instancia y se realizará automáticamente su lógica después de fotogramas hasta su finalización.

Cada uno de los tipos mencionados anteriormente termina con la palabra *a* lo que significa que la `CCAction` modificará el `CCNode` para que el valor del argumento representa el estado final cuando ha finalizado la acción. Por ejemplo, al crear un `CCMoveTo` con una posición de X = 100 e Y = 200 produce el `CCNode` la instancia `Position` se establece en X = 100, Y = 200 al final de la hora especificada, independientemente de la `CCNode` ubicación inicial de la instancia.

Cada una "A" de la clase también tiene una versión "Por", que se agregará el valor del argumento en el valor actual en el `CCNode`. Por ejemplo, al crear un `CCMoveBy` con una posición de X = 100 e Y = 200 dará como resultado la `CCNode` instancia que se mueven a las unidades de la derecha 100 y 200 unidades desde la posición en que estaba cuando se inició la acción.


## <a name="easing-actions"></a>Acciones de aceleración

De forma predeterminada, llevará a cabo las acciones de cambio a la variable *la interpolación lineal* : la acción se moverá hacia el valor deseado a una velocidad constante. Si interpolar *posición* linealmente, el objeto en movimiento inmediatamente iniciará y detendrá mover al principio y al final de la acción, y su velocidad permanecerá constante mientras se ejecuta la acción. 

La interpolación lineal no es menos discordante y agrega un elemento de polaco, por lo que CocosSharp ofrece una variedad de aceleración las acciones que pueden utilizarse para modificar las acciones de cambio a la variable.

En el **ActionProject** ejemplo, podemos cambiar entre estos tipos de entradas y salidas de acciones, haga clic en la segunda etiqueta (que de forma predeterminada **<None>**):

![](ccaction-images/image4.gif "El usuario puede alternar entre estos tipos de entradas y salidas de acciones, haga clic en la segunda etiqueta")

Aceleración acciones son especialmente eficaces porque no están enlazados a una acción de configuración de variable determinada. Esto significa que puede utilizarse la misma acción de aceleración para asignar posición, rotación, escala o acciones personalizadas (tal y como se mostrará más adelante en esta guía).

Aceleración acciones ajustan las acciones de configuración de variable (siempre y cuando la acción de configuración de la variable que se hereda de `CCFiniteTimeAction`) al aceptar una acción de configuración de variable como argumento en sus constructores.

Por ejemplo, si las etiquetas se establecen en **posición**, **CCEaseElastic**, a continuación, el siguiente código se ejecutará cuando se detecta un toque (tenga en cuenta que el código se ha omitido para resaltar las líneas correspondientes):


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

Como se muestra en la aplicación, la aceleración mismo exacto puede aplicarse a otras acciones de configuración de variable como `CCRotateTo`:

![](ccaction-images/image5.gif "La aceleración mismo exacto puede aplicarse a otras acciones de configuración de variable como CCRotateTo")


## <a name="easing-in-out-and-inout"></a>Aceleración In, Out e InOut

Todas las acciones de aceleración tener `In`, `Out`, o `InOut` anexado al tipo de aceleración. Estos términos hacen referencia a cuando se aplica la aceleración: `In` significa que facilita que se aplicarán al principio, `Out` significa que al final, y `InOut` significa tanto al principio y al final.

Un `In` cómo facilitar la acción afecta a la manera en que una variable se aplica a lo largo de la interpolación toda (tanto al principio y al final), pero normalmente las características más reconocibles de la aceleración acción llevará a cabo al principio. De forma similar, `Out` acciones aceleración se caracterizan por su comportamiento al final de la interpolación. Por ejemplo, `CCEaseBounceOut` dará como resultado en un objeto que se devuelven al final de la acción.


### <a name="out"></a>Out

`Out` aceleración generalmente aplica los cambios más notorios al final de la interpolación. Por ejemplo, `CCEaseExponentialOut` ralentizará la tasa de cambio de la variable variable medida que se acerque el valor de destino:

![](ccaction-images/image6.gif "CCEaseExponentialOut ralentizará la tasa de cambio de la variable variable a medida que se acerque el valor de destino")


### <a name="in"></a>En

`In` aceleración por lo general, aplica el cambio más notable al principio de la interpolación. Por ejemplo, `CCEaseExponentialIn` se moverá más lenta al principio de la acción:

![](ccaction-images/image7.gif "CCEaseExponentialIn moverá más lenta al principio de la acción")


### <a name="inout"></a>InOut

`InOut` por lo general, aplica los cambios más notorios tanto al principio y al final. `InOut` aceleración es normalmente simétrica. Por ejemplo, `CCEaseExponentialInOut` se moverá lenta al principio y al final de la acción:

![](ccaction-images/image8.gif "CCEaseExponentialInOut moverá lenta al principio y al final de la acción")


## <a name="implementing-a-custom-ccaction"></a>Implementar un CCAction personalizado

Todas las clases que hemos hablado hasta ahora se incluyen en CocosSharp para proporcionar funcionalidad común. Personalizado `CCAction` implementaciones pueden proporcionar flexibilidad adicional. Por ejemplo, un `CCAction` que controla la relación de rellena de una barra de experiencia puede usarse para que la barra de la experiencia sin problemas aumenta cada vez que el usuario gana experiencia.

`CCAction` las implementaciones normalmente requieren dos clases:

 - `CCFiniteTimeAction` implementación: la clase de acción de tiempo finito es responsable de iniciar la acción. Es la clase que se crea una instancia y cualquiera agregado directamente a un `CCNode` o a una acción de entradas y salida. Debe crear una instancia y devolver un `CCFiniteTimeActionState`, lo que llevará a cabo las actualizaciones.
 - `CCFiniteTimeActionState` implementación: la clase de estado de acción de tiempo finito es responsable de actualizar las variables implicadas en la acción. Debe implementar una función de actualización, que asigna el valor en el destino de acuerdo con un valor de hora. Esta clase no se hace referencia a forma explícita fuera de la `CCFiniteTimeAction` que lo crea. Simplemente funciona "en segundo plano".

**ActionProject** proporciona un personalizado `CCFiniteTimeAction` implementación denominada `LineWidthAction,` que se utiliza para ajustar el ancho de la línea amarilla que se dibujan en la parte superior del círculo rojo. Tenga en cuenta que su trabajo solo es crear instancias y devolver un `LineWidthState` instancia:


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

Como se mencionó anteriormente, el `LineWidthState` realiza el trabajo de asignación de la línea `Width` propiedad de acuerdo con la cantidad `time` ha pasado:


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

El LineWidthAction puede combinarse con cualquier acción de aceleración para cambiar el ancho de línea de varias maneras, como se muestra en la siguiente animación:

![](ccaction-images/image9.gif "El LineWidthAction puede combinarse con cualquier acción de aceleración para cambiar el ancho de línea de varias maneras, como se muestra en esta animación")


### <a name="interpolation-and-the-update-method"></a>La interpolación y el método de actualización

La lógica única, además de almacenar valores en las clases anteriores, reside en el `LineWidthState.Update` método. El `startWidth` variable almacena el ancho del destino `LineNode` al principio de la acción y el `deltaWidth` variable almacena el valor de la cantidad cambiará a lo largo de la acción.

Al sustituir el `time` variable con un valor de 0, podemos ver que el destino `LineNode` estará en su posición inicial:


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

De igual forma, podemos ver que el destino `LineNode` estará a su destino mediante la sustitución de la variable de tiempo con un valor de 1:


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

El `time` valor normalmente estará comprendido entre 0 y 1 - pero no siempre - y `Update` implementaciones no deben suponer estos límites. Algunos métodos de aceleración (como `CCEaseBackIn` y `CCEaseBackOut`) proporcionará un valor de tiempo fuera del intervalo de 0 a 1.


## <a name="conclusion"></a>Conclusión

La interpolación y aceleración son una parte fundamental de la creación de un juego pulido, especialmente al crear interfaces de usuario. Esta guía describe cómo usar `CCActions` interpolar valores estándar como la posición y rotación, así como valores personalizados. El `LineWidthState` y `LineWidthAction` clases muestran cómo implementar una acción personalizada.

## <a name="related-links"></a>Vínculos relacionados

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [Ejemplo completo](https://developer.xamarin.com/samples/mobile/CCAction/)
