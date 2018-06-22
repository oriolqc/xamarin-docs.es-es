---
title: Operaciones matemáticas 2D con CocosSharp
description: Esta guía incluye matemáticas 2D para desarrollo de juegos. Usa CocosSharp para mostrar cómo realizar tareas comunes de desarrollo de juegos y explica las matemáticas que hay detrás de estas tareas.
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 13279df69b7a22117c10d74f1a15c082aff13264
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921591"
---
# <a name="2d-math-with-cocossharp"></a>Operaciones matemáticas 2D con CocosSharp

_Esta guía incluye matemáticas 2D para desarrollo de juegos. Usa CocosSharp para mostrar cómo realizar tareas comunes de desarrollo de juegos y explica las matemáticas que hay detrás de estas tareas._

Para colocar y mover objetos con el código es una parte fundamental del desarrollo de juegos de todos los tamaños. Colocar y mover requieren el uso de matemáticas, si un juego requiere mover un objeto a lo largo de una línea recta ni el uso de trigonométricas para rotación. Este documento tratará los siguientes temas:

 - Progreso
 - Aceleración
 - Rotación de objetos CocosSharp
 - Usando la rotación con velocidad

Los desarrolladores que no tienen un fondo de matemáticas seguro o que haya long-olvidado estos temas de la escuela, no es necesario preocuparse de si: este documento interrumpirá conceptos hacia abajo en partes de pequeño tamaño y se adjuntará a explicaciones teóricos con ejemplos prácticos. En resumen, este artículo responde a la pregunta de estudiantes de matemáticas antiguo: "Cuando se realmente necesito hacer todo esto?"


## <a name="requirements"></a>Requisitos

Aunque este documento se centra principalmente en el lado matemático de CocosSharp, ejemplos de código supone trabajar con objetos heredar formulario `CCNode`. Además, desde `CCNode` no incluye valores de velocidad y la aceleración, el código supone trabajar con entidades que proporcionan valores como VelocityX, VelocityY, AccelerationX y AccelerationY. Para obtener más información sobre las entidades, consulte nuestro tutorial en [entidades en CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="velocity"></a>Progreso

Los desarrolladores de juegos usa el término *velocidad* para describir cómo un objeto es móvil – específicamente rapidez con que algo se mueve y la dirección que esté móvil. 

Velocidad se define mediante dos tipos de unidades: una unidad de posición y una unidad de tiempo. Por ejemplo, velocidad del automóvil se define como millas por hora o kilómetros por hora. Desarrolladores de juegos a menudo uso píxeles por segundo para definir la rapidez con que un objeto se mueve. Por ejemplo, puede mover una viñeta a una velocidad de 300 píxeles por segundo. Es decir, si una viñeta se está moviendo en 300 píxeles por segundo, a continuación, habrá mueven 600 unidades en dos segundos y 900 unidades en tres segundos y así sucesivamente. Por lo general, el valor de progreso *agrega* a la posición de un objeto (como se verá más adelante).

Aunque hemos usado velocidad para explicar las unidades de velocidad, la velocidad de término suele hacer referencia a un valor independiente de dirección, mientras que la velocidad de término hace referencia a la velocidad y la dirección. Por lo tanto, la asignación de velocidad de viñeta (suponiendo que viñeta es una clase que incluye las propiedades necesarias) puede ser similar al siguiente:


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>Progreso de implementación

CocosSharp no lleva a cabo la velocidad, por lo que deben implementar su propia lógica de movimiento que requieren el movimiento de objetos. Los desarrolladores de juegos nueva implementación de progreso suele cometen el de hacer que su progreso depende de la velocidad de fotogramas. Es decir, el siguiente *implementación incorrecta* le resultará proporcionar resultados correctos, pero se basará en la velocidad de fotogramas del juego:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

Si el juego se ejecuta en una velocidad de fotogramas superior (por ejemplo, 60 fotogramas por segundo en lugar de 30 fotogramas por segundo), el objeto aparecerá moverse más rápido que si se ejecuta en una velocidad de fotogramas más lenta. De forma similar, si el juego es no se puede procesar fotogramas en tan alto de una velocidad de fotogramas (que puede deberse a procesos en segundo plano con los recursos del dispositivo), el juego se definirán ralentice.

Para tener esto en cuenta, velocidad se implementa a menudo con un valor de hora. Por ejemplo, si la `seconds` variable representa el número (o la fracción) de segundos desde que se aplicó la última velocidad de tiempo, a continuación, el código siguiente, se crearán en el objeto con el movimiento coherente, independientemente de la velocidad de fotogramas:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

Tenga en cuenta que un juego que se ejecuta en una menor velocidad de fotogramas se actualizará con frecuencia la posición de los objetos menos. Por lo tanto, cada actualización se producirá en los objetos avanzando cada vez más a como lo harían si el juego se estaban actualizando con más frecuencia. El `seconds` valor cuentas para ello, cuánto tiempo ha transcurrido desde la última actualización de informes.

Para obtener un ejemplo de cómo agregar el movimiento, basándose en el tiempo, consulte [Esta receta que abarcan el tiempo en función de movimiento](https://developer.xamarin.com/recipes/cross-platform/game_development/time_based_movement/).


### <a name="calculating-positions-using-velocity"></a>Calcular posiciones utilizando progreso

Progreso puede utilizarse para realizar predicciones sobre donde un objeto quedará cierta cantidad de paso del tiempo, o para ayudar a optimizar el comportamiento de los objetos sin necesidad de ejecutar el juego. Por ejemplo, un desarrollador que está implementando el movimiento de un punto de activarse necesita establecer la velocidad de viñeta después de que se crea una instancia. El tamaño de pantalla puede utilizarse para proporcionar una base para establecer el progreso. Es decir, si el programador sabe el punto de cambiar el alto de la pantalla en 2 segundos, el progreso se debe establecer en el alto de la pantalla dividido por 2. Si la pantalla es 800 píxeles de alto, velocidad de viñeta se establecería en 400 (que es 800/2).

De forma similar, lógica de juego que necesite calcular el tiempo que se tarda un objeto para llegar a un destino dado su progreso. Esto se puede calcular dividiendo la distancia progreso del objeto viaje. Por ejemplo, el código siguiente muestra cómo asignar texto a una etiqueta que muestra cuánto tiempo hasta que un misiles llega a su destino:


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>Aceleración

*Aceleración* es un concepto común en el desarrollo de juego y comparte muchas similitudes con velocidad. Aceleración cuantifica si un objeto es lo que acelera la o ralentizar (cómo cambia el valor de progreso en el tiempo). Aceleración *agrega* a velocidad, al igual que agrega velocidad para colocar. Aplicaciones comunes de la aceleración de incluyen gravedad, un automóvil acelerar y una nave espacial desencadenar sus thrusters. 

Similar a velocidad, aceleración se define en una posición y una unidad de tiempo; Sin embargo, unidad de tiempo de aceleración se conoce como un *cuadrático* unidad, que refleja cómo se define la aceleración matemáticamente. Es decir, aceleración de juego a menudo se mide en *píxeles por segundo al cuadrado*.

Si un objeto tiene una aceleración X de 10 unidades por segundo al cuadrado, que significa que su progreso aumentará por 10 cada segundo. Si desde paralizar, después de un segundo será móvil 10 unidades por segundo, después de dos segundos 20 unidades por segundo, y así sucesivamente.

Aceleración en dos dimensiones requiere un componente de X e Y, por lo que se puede asignar como sigue:


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>Aceleración frente a desaceleración

Aunque a veces se diferencian aceleración y desaceleración de voz de cada día, no hay ninguna diferencia entre los dos técnica. Gravedad es un equipo que da lugar a la aceleración. Si un objeto se produce hacia arriba, a continuación, gravedad serán más lentas (desacelerando), pero una vez que el objeto se ha detenido Aumente y está acumulando en la misma dirección que la gravedad, a continuación, gravedad es acelerarlo (acelerando). Tal y como se muestra a continuación, la aplicación de una aceleración es el mismo si se aplica en la misma dirección de dirección o contrario del movimiento. 


### <a name="implementing-acceleration"></a>Implementación de aceleración

Aceleración es similar a velocidad cuando se implementa: se implementa automáticamente por CocosSharp y aceleración basada en el tiempo es la implementación deseada (en lugar de aceleración basados en marcos). Por lo tanto, puede parecer una implementación simple de aceleración (junto con la velocidad):

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

El código anterior es lo que se conoce como un *aproximación lineal* para la implementación de aceleración. De hecho, implementa aceleración con un grado bastante estrecha de precisión, pero no es un modelo preciso perfectamente de aceleración. Se incluye anteriormente para ayudar a explicar el concepto de cómo se implementa la aceleración.

La implementación siguiente es una aplicación matemáticamente precisa de aceleración y progreso:


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

La diferencia más obvia reside en el código anterior es la `halfSecondsSquared` variable y su uso para aplicar la aceleración para colocar. El motivo matemático queda fuera del ámbito de este tutorial, pero los desarrolladores interesados en la expresión matemática detrás de esto pueden encontrar más información en [esta explicación sobre la integración de aceleración.](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

El efecto práctico de `halfSecondSquare` es que la aceleración se comportará matemáticamente precisa y predecible, independientemente de la velocidad de fotogramas. La aproximación lineal de la aceleración de está sujeto a la velocidad de fotogramas: cuanto menor sea la velocidad de fotogramas cae se convierte en la aproximación menos precisos. Usar `halfSecondsSquared` garantías que código comportará igual independientemente de la velocidad de fotogramas.


## <a name="angles-and-rotation"></a>Ángulos y rotación

Visual objetos como `CCSprite` admite la rotación a través de un `Rotation` variable. Esto puede asignarse a un valor para establecer su rotación en grados. Por ejemplo, el código siguiente muestra cómo girar un `CCSprite` instancia:


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

Esto genera lo siguiente:

![](math-images/image1.png "Esto da como resultado en esta captura de pantalla")

Tenga en cuenta que la rotación se lleva 45 grados a la derecha (que, por motivos históricos, es el opuesto de cómo se aplica el giro matemáticamente):

![](math-images/image2.png "Tenga en cuenta que la rotación se lleva a la derecha de 45 grados que por motivos históricos es el opuesto de cómo se aplica el giro matemáticamente")

Por lo general giro CocosSharp y matemáticas normales se puede visualizar como sigue:

![](math-images/image3.png "Por lo general se puede visualizar en giro CocosSharp y matemáticas regular similar al siguiente")

![](math-images/image4.png "Por lo general se puede visualizar en giro CocosSharp y matemáticas regular similar al siguiente")

Esta distinción es importante porque el `System.Math` clase usa la rotación a la izquierda, por lo que los programadores familiarizados con esta clase se deben invertir los ángulos cuando se trabaja con `CCNode` instancias.

Debemos mencionar que los diagramas anteriores muestran rotación en grados; Sin embargo, algunas funciones matemáticas (por ejemplo, las funciones de la `System.Math` espacio de nombres) esperar y devolver valores en *radianes* en lugar de grados. Analizaremos cómo convertir entre los tipos de dos unidad más adelante en esta guía.


### <a name="rotating-to-face-a-direction"></a>Para que se enfrentan a una dirección de la rotación

Como se indicó anteriormente, `CCSprite` se pueden girar utilizando el `Rotation` propiedad. El `Rotation` propiedad se proporciona por `CCNode` (la clase base para `CCSprite`), lo que significa la rotación se puede aplicar a las entidades que heredan de `CCNode` también. 

Algunos juegos requieren objetos va a rotar por lo que enfrentan a un destino. Algunos ejemplos son un enemigo controlados por el equipo con un destino de Reproductor o una nave espacial Volar hacia el punto donde el usuario toca la pantalla. Sin embargo, un valor de rotación en primer lugar se debe calcular basándose en la ubicación de la entidad que se giran y la ubicación de destino para que se enfrentan.

Este proceso requiere una serie de pasos:

 - Identificar la *desplazamiento* del destino. Desplazamiento hace referencia a la distancia de X e Y entre las entidades de la rotación y la entidad de destino.
 - Calcular el ángulo desde el desplazamiento mediante la función de trigonométricas arco tangente (se explica en detalle a continuación).
 - Ajuste de una diferencia entre 0 grados apuntando hacia la derecha y la dirección que señala de la entidad de la rotación cuando sin girado.
 - Ajuste de la diferencia entre matemática rotación (izquierda) y la rotación de CocosSharp (izquierda).

La siguiente función (supone que se escriban en una entidad) gira la entidad que se enfrentan a un destino:


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

El código anterior podría usarse para girar una entidad para que se enfrenta el punto donde el usuario toca la pantalla, como se indica a continuación:


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

Este código produce el siguiente comportamiento:

![](math-images/image5.gif "Este código produce este comportamiento")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>Usar Atan2 para convertir los desplazamientos a ángulos

`System.Math.Atan2` puede usarse para convertir un desplazamiento de un ángulo. El nombre de la función `Atan2` proceden el arco tangente de la función trigonométrica. El sufijo "2" diferencia esta función de la norma `Atan` función, que estrictamente coincide con el comportamiento matemático de arco tangente. Arco tangente es una función que devuelve un valor entre -90 y + 90 grados (o su equivalente en radianes). Muchas aplicaciones, incluidos los juegos informáticos, a menudo, requieren un total de 360 grados de valores, por lo que la `Math` clase incluye `Atan2` para satisfacer esta necesidad.

Tenga en cuenta que el código anterior pasa el parámetro Y en primer lugar, a continuación, el parámetro X, al llamar a la `Atan2` método. Se trata con versiones anteriores de la habitual X, Y ordenación de las coordenadas de posición. Para obtener más información [consulte los documentos de Atan2](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx).

También merece la pena indicar que el valor devuelto desde `Atan2` es en radianes, que es otra unidad que se usa para medir ángulos. Esta guía no se tratarán los detalles de radianes, pero tenga en cuenta que todas las funciones trigonométricas en el `System.Math` espacio de nombres use radianes, por lo que los valores deben convertirse en grados antes de que se utilizan en objetos de CocosSharp. Puede encontrar más información en radianes [en la página de Wikipedia Radián](http://en.wikipedia.org/wiki/Radian).

#### <a name="forward-angle"></a>Ángulo hacia delante

Una vez el `FacePoint` método convierte el ángulo en radianes, definen un `forwardAngle` valor. Este valor representa el ángulo en el que la entidad está sufriendo cuando su valor de rotación es igual a 0. En este ejemplo, se supone que la entidad está sufriendo hacia arriba, que es 90 grados cuando se usa un giro matemático (en lugar de la rotación de CocosSharp). Utilizamos la rotación matemática aquí porque se aún no hemos invertido la rotación de CocosSharp.

La siguiente muestra qué una entidad con un `forwardAngle` de 90 grados podría ser similar:

![](math-images/image6.png "Esto muestra el aspecto que podría tener una entidad con un forwardAngle de 90 grados")


### <a name="angled-velocity"></a>Velocidad en ángulo

Hasta ahora, hemos analizado cómo convertir un desplazamiento en un ángulo. En esta sección queda viceversa: toma un ángulo y lo convierte en X y los valores y. Algunos ejemplos comunes son un coche que se mueve en la dirección en la que está sufriendo o una nave espacial con una viñeta que se mueve en la dirección que está orientada hacia el envío. 

Conceptualmente, se puede calcular el progreso, definir la velocidad deseada cuando sin girado y rotación de esa velocidad el ángulo que se enfrenta a una entidad. Para ayudar a explicar este concepto, se pueden visualizar progreso (y la aceleración) como un 2 dimensional *vector* (que es normalmente se dibuja como una flecha). Un vector de un valor de progreso con X = 100 e Y = 0 se pueden visualizar como sigue:

![](math-images/image7.png "Un vector de un valor de progreso con X = 100 e Y = 0 se pueden visualizar como se indica a continuación.")

Este vector se puede girar para dar lugar a una velocidad nuevo. Por ejemplo, girar el vector de 45 grados (usando la rotación a la izquierda) genera lo siguiente:

![](math-images/image8.png "Girar el vector de 45 grados con resultados de la rotación a la izquierda en este")

Afortunadamente, velocidad, aceleración y posición incluso todos se pueden girar con el mismo código sin tener en cuenta cómo se aplican los valores. La siguiente función de uso general se puede utilizar para girar un vector por un valor de rotación de CocosSharp:


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

Un conocimiento completo de la `RotateVector` método requiere que se está familiarizado con las funciones trigonométricas coseno y seno junto con algunos álgebra lineal, que queda fuera del ámbito de este artículo. Sin embargo, una vez implementado el `RotateVector` método se puede utilizar para girar cualquier vector, incluido un vector de velocidad. Por ejemplo, el código siguiente puede activar una viñeta en una dirección especificada por el `rotation` valor:


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

Este código puede producir similar:

![](math-images/image9.png "Este código puede producir algo parecido a esta captura de pantalla")


## <a name="summary"></a>Resumen

Esta guía trata conceptos matemáticos comunes de desarrollo de juegos en 2D. Muestra cómo asignar e implemente la velocidad y aceleración y explica cómo rotar los objetos y los vectores de movimiento en cualquier dirección.
