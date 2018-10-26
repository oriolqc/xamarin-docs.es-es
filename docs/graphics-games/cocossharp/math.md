---
title: Matemáticas 2D con CocosSharp
description: Esta guía trata las matemáticas 2D para el desarrollo de juegos. Usa CocosSharp para mostrar cómo realizar tareas comunes de desarrollo de juegos y explica las matemáticas detrás de estas tareas.
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 60386b3629e8ed9d2fd1ff165cd2c04d9571b51a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106239"
---
# <a name="2d-math-with-cocossharp"></a>Matemáticas 2D con CocosSharp

_Esta guía trata las matemáticas 2D para el desarrollo de juegos. Usa CocosSharp para mostrar cómo realizar tareas comunes de desarrollo de juegos y explica las matemáticas detrás de estas tareas._

Para colocar y mover objetos con el código es una parte fundamental de desarrollo de juegos de todos los tamaños. Colocar y mover requieren el uso de matemáticas, si un juego requiere mover un objeto a lo largo de una línea recta, o el uso de trigonometría de rotación. Este documento trata los temas siguientes:

 - Progreso
 - Aceleración
 - Girar objetos CocosSharp
 - Usando la rotación con progreso

Los desarrolladores que no tienen un fondo de matemáticas seguro o que han olvidados estos temas desde la escuela, no es necesario preocuparse: este documento interrumpirá conceptos hacia abajo en partes de pequeño tamaño y se adjuntará a explicaciones teóricas con ejemplos prácticos. En resumen, este artículo responderá a la pregunta de matemáticas viejos estudiantes: "Cuando en realidad necesito utilizar este material?"


## <a name="requirements"></a>Requisitos

Aunque este documento se centra principalmente en el lado de CocosSharp matemático, ejemplos de código suponen trabajar con objetos heredar formulario `CCNode`. Además, desde `CCNode` no incluyen valores para la velocidad y la aceleración, el código supone trabajar con entidades que proporcionan valores como VelocityX, VelocityY, AccelerationX y AccelerationY. Para obtener más información sobre las entidades, consulte nuestro tutorial en [entidades de CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="velocity"></a>Progreso

Los desarrolladores de juegos usa el término *velocity* para describir cómo un objeto está moviendo – específicamente rapidez algo se mueve y la dirección que sea móvil. 

Velocidad se define mediante dos tipos de unidades: una unidad de posición y una unidad de tiempo. Por ejemplo, la velocidad de un automóvil se define como millas por hora o kilómetros por hora. Desarrolladores de juegos se mueve a menudo uso píxeles por segundo para definir la velocidad es un objeto. Por ejemplo, puede mover una viñeta a una velocidad de 300 píxeles por segundo. Es decir, si una viñeta está migrando en 300 píxeles por segundo, a continuación, se habrá mover 600 unidades en dos segundos y 900 unidades en tres segundos y así sucesivamente. Por lo general, el valor de velocidad *agrega* a la posición de un objeto (como veremos a continuación).

Aunque hemos usado la velocidad para explicar las unidades de velocidad, la velocidad de término suele hacer referencia a un valor independiente de dirección, mientras que la velocidad de término hace referencia a la velocidad y la dirección. Por lo tanto, la asignación de velocidad de viñeta (suponiendo de viñeta es una clase que incluye las propiedades necesarias) puede tener este aspecto:


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>Velocidad de implementación

CocosSharp no implementa la velocidad, por lo que los objetos que requieren movimiento deberá implementar su propia lógica de movimiento. Los desarrolladores de juegos nuevos implementa de velocity a menudo cometen el error de hacer que su velocidad depende de la velocidad de fotogramas. Es decir, la siguiente *implementación incorrecta* parecerán proporcionar resultados correctos, pero se basará en la velocidad de fotogramas del juego:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

Si el juego se ejecuta en una velocidad de fotogramas superior (por ejemplo, 60 fotogramas por segundo en lugar de 30 fotogramas por segundo), aparecerá el objeto mover más rápido que si está ejecutando en una velocidad de fotogramas más lenta. De forma similar, si el juego no se puede procesar fotogramas en tan alto de una velocidad de fotogramas (que puede deberse a procesos en segundo plano con los recursos del dispositivo), aparecerá el juego que se ralentice.

Para resolverlo, velocidad suele implementarse mediante un valor de hora. Por ejemplo, si la `seconds` variable representa el número (o fracción) de segundos desde que se aplicó la última velocidad del tiempo, a continuación, daría como resultado el siguiente código en el objeto de movimiento coherente, independientemente de la velocidad de fotogramas:

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

Considere la posibilidad de que un juego que se ejecuta en una menor velocidad de fotogramas actualizará con frecuencia la posición de sus objetos menos. Por lo tanto, cada actualización dará como resultado los objetos avanzando cada vez más de la que tendrían si el juego se actualiza con más frecuencia. El `seconds` valor cuentas para esto, ¿cuánto tiempo ha transcurrido desde la última actualización de la creación de informes.

Para obtener un ejemplo de cómo agregar movimiento basado en el tiempo, consulte [Esta receta que abarcan el tiempo en función de movimiento](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/game_development/time_based_movement).


### <a name="calculating-positions-using-velocity"></a>Posiciones con velocidad de cálculo

Velocidad puede utilizarse para realizar predicciones sobre donde un objeto será después de cierta cantidad de tiempo, o para ayudar a optimizar el comportamiento de los objetos sin necesidad de ejecutar el juego. Por ejemplo, un desarrollador que está implementando el movimiento de una viñeta desencadenada debe establecer la velocidad de la viñeta después de que se crea una instancia. El tamaño de pantalla puede utilizarse para proporcionar una base para establecer la velocidad. Es decir, si el desarrollador sabe la viñeta debe mover el alto de la pantalla de 2 segundos, se debe establecer la velocidad a la altura de la pantalla dividida entre 2. Si la pantalla es de 800 píxeles de alto, velocidad de la viñeta se establecería en 400 (que es 800/2).

De forma similar, lógica de juego que necesite calcular cuánto tardará llegar a un destino dado su velocidad de un objeto. Esto puede calcularse dividiendo la distancia de viaje por la velocidad de viaje del objeto. Por ejemplo, el código siguiente muestra cómo asignar texto a una etiqueta que muestra cuánto tiempo hasta que un misiles alcance su destino:


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>Aceleración

*Aceleración* es un concepto común en el desarrollo de juegos, y lo comparte muchas similitudes con la velocidad. Aceleración cuantifica si un objeto es acelerar o ralentizar (cómo cambia el valor de la velocidad con el tiempo). Aceleración *agrega* a velocidad, tal como la velocidad se agrega a la posición. Aplicaciones comunes de aceleración incluyen gravedad, un vehículo acelerar y una nave espacial activar sus thrusters. 

Al igual que la velocidad, la aceleración se define en una posición y una unidad de tiempo; Sin embargo, unidad de tiempo de aceleración se conoce como un *cuadradas* unidad, lo que refleja cómo se define la aceleración matemáticamente. Es decir, la aceleración de juegos a menudo se mide en *píxeles por segundo al cuadrado*.

Si un objeto tiene una aceleración de 10 unidades por segundo al cuadrado, que significa que su velocidad aumentarán en un 10 por segundo. Si a partir de una parada, después de un segundo estará migrando en 10 unidades por segundo, después de dos segundos 20 unidades por segundo, y así sucesivamente.

Aceleración en dos dimensiones requiere un componente de X e Y, por lo que puede asignarse como sigue:


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>Aceleración o desaceleración

Aunque la aceleración y la desaceleración a veces se diferencian en voz cada día, no hay ninguna diferencia entre las dos técnica. La gravedad es una fuerza que da como resultado la aceleración. Si un objeto se produce hacia arriba, a continuación, gravedad ralentizará lo (decelerar), pero una vez que el objeto de escalada se ha detenido y se queda en la misma dirección como la gravedad, a continuación, la gravedad es acelerarlo (acelerar). Como se muestra a continuación, la aplicación de una aceleración es el mismo si se aplica en la misma dirección de dirección o lo opuesto de movimiento. 


### <a name="implementing-acceleration"></a>Implementación de aceleración

Aceleración es similar a velocidad cuando se implementa: se implementa automáticamente por CocosSharp y aceleración basada en el tiempo es la implementación deseada (en lugar de la aceleración basada en marco). Por lo tanto, una implementación simple de aceleración (junto con la velocidad) puede ser similar:

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

El código anterior es lo que se conoce como un *aproximación lineal* para la implementación de aceleración. De hecho, implementa aceleración con un grado bastante estrecha de precisión, pero no es un modelo perfectamente exacta de aceleración. Se incluye anteriormente para ayudar a explicar el concepto de cómo se implementa la aceleración.

La siguiente implementación es una aplicación matemáticamente precisa de aceleración y velocidad:


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

La diferencia más obvia en el código anterior es el `halfSecondsSquared` variable y su uso para aplicar la aceleración en posición. La razón de esto matemática queda fuera del ámbito de este tutorial, pero los desarrolladores interesados en las matemáticas detrás de esto pueden encontrar más información en [esta discusión sobre la integración de aceleración.](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

La repercusión práctica del `halfSecondSquare` es que la aceleración se comportará matemáticamente precisa y predecible, independientemente de la velocidad de fotogramas. La aproximación lineal de aceleración está sujeta a la velocidad de fotogramas: cuanto menor sea la velocidad de fotogramas cae se convierte en la aproximación menos preciso. Uso de `halfSecondsSquared` garantiza que el código comportará igual independientemente de la velocidad de fotogramas.


## <a name="angles-and-rotation"></a>Ángulos y la rotación

Objetos visuales, como `CCSprite` admiten el giro a través de un `Rotation` variable. Pueden asignarse a un valor para establecer su rotación en grados. Por ejemplo, el código siguiente muestra cómo girar un `CCSprite` instancia:


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

Esto da como resultado lo siguiente:

![](math-images/image1.png "Esto da como resultado de esta captura de pantalla")

Tenga en cuenta que la rotación es de 45 grados a la derecha (que, por motivos históricos, es el opuesto de cómo se aplica el giro matemáticamente):

![](math-images/image2.png "Tenga en cuenta que la rotación es 45 grados a la derecha que por motivos históricos es el opuesto de cómo se aplica el giro matemáticamente")

En general rotación de CocosSharp matemáticas y ciencias normal se puede visualizar como sigue:

![](math-images/image3.png "En general se puede visualizar la rotación de CocosSharp y regular matemáticas similar al siguiente")

![](math-images/image4.png "En general se puede visualizar la rotación de CocosSharp y regular matemáticas similar al siguiente")

Esta distinción es importante porque el `System.Math` clase usa la rotación a la izquierda, por lo que los desarrolladores familiarizados con esta clase necesitan invertir ángulos cuando se trabaja con `CCNode` instancias.

Debemos mencionar que los diagramas anteriores muestran la rotación en grados; Sin embargo, algunas funciones matemáticas (por ejemplo, las funciones de la `System.Math` espacio de nombres) esperan y devuelven los valores de *radianes* en lugar de grados. Echemos un vistazo cómo convertir entre los tipos de dos unidad más adelante en esta guía.


### <a name="rotating-to-face-a-direction"></a>Para enfrentar una dirección de la rotación

Como se indicó anteriormente, `CCSprite` se pueden girar mediante la `Rotation` propiedad. El `Rotation` propiedad proporcionado por `CCNode` (la clase base para `CCSprite`), lo que significa la rotación se puede aplicar a las entidades que heredan de `CCNode` también. 

Algunos juegos requieren los objetos que se girarán por lo que enfrentan a un destino. Algunos ejemplos son un enemigo controlados por el equipo en un destino jugador o una nave espacial Volar hacia el punto donde el usuario toca la pantalla de la solución. Sin embargo, un valor de rotación en primer lugar se debe calcular basándose en la ubicación de la entidad que se giran y la ubicación de destino para hacer frente a.

Este proceso requiere una serie de pasos:

 - Que identifica el *desplazamiento* del destino. Desplazamiento hace referencia a la distancia de X e Y entre la entidad de la rotación y la entidad de destino.
 - Calcular el ángulo desde el desplazamiento mediante la función de trigonometría arco tangente (se explica en detalle a continuación).
 - Ajuste de una diferencia entre el 0 grados apuntando hacia la derecha y la dirección en que los puntos de la entidad de la rotación cuando sin girado.
 - Ajustar la diferencia entre la rotación matemática (izquierda) y la rotación de CocosSharp (hacia la derecha).

La función siguiente (que se supone que se escriben en una entidad) gira la entidad que se enfrentan a un destino:


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

El código anterior podría usarse para girar una entidad, por lo que se enfrenta el punto donde el usuario toca la pantalla, como se indica a continuación:


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

Este código produce el siguiente comportamiento:

![](math-images/image5.gif "Este código produce este comportamiento")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>Uso de Atan2 para convertir los desplazamientos a ángulos

`System.Math.Atan2` puede usarse para convertir un desplazamiento en un ángulo. El nombre de función `Atan2` procede el arco tangente de la función trigonométrica. El sufijo "2" diferencia esta función de la norma `Atan` función, que coincide estrictamente con el comportamiento matemático de arco tangente. Arco tangente es una función que devuelve un valor entre -90 y + 90 grados (o su equivalente en radianes). Muchas aplicaciones, como juegos informáticos, a menudo requieren una completa de 360 grados de valores, por lo que la `Math` incluye la clase `Atan2` para satisfacer esta necesidad.

Tenga en cuenta que el código anterior pasa el parámetro Y en primer lugar, a continuación, el parámetro X, al llamar a la `Atan2` método. Se trata con versiones anteriores de la habitual X, Y ordenación de las coordenadas de posición. Para obtener más información [consulte los documentos Atan2](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx).

También merece la pena tener en cuenta que el valor devuelto desde `Atan2` está en radianes, que es otra unidad que se usa para medir los ángulos. Esta guía no se explican los detalles de radianes, pero tenga en cuenta que todas las funciones trigonométricas en el `System.Math` espacio de nombres use radianes, por lo que los valores deben convertirse a grados antes de que se usa en los objetos de CocosSharp. Puede encontrar más información en radianes [en la página de Wikipedia en radianes](http://en.wikipedia.org/wiki/Radian).

#### <a name="forward-angle"></a>Ángulo hacia delante

Una vez el `FacePoint` método convierte el ángulo en radianes, que define un `forwardAngle` valor. Este valor representa el ángulo en el que se enfrenta la entidad cuando su valor de rotación es igual a 0. En este ejemplo, se supone que la entidad está experimentado hacia arriba, que es de 90 grados cuando se usa una rotación matemática (en lugar de la rotación de CocosSharp). Usamos la rotación matemática aquí porque hemos aún no hemos invertido el giro de CocosSharp.

La siguiente muestra lo que una entidad con un `forwardAngle` de 90 grados, podría ser similar a:

![](math-images/image6.png "Esto muestra el aspecto que podría tener una entidad con un forwardAngle de 90 grados")


### <a name="angled-velocity"></a>Velocidad en ángulo

Hasta ahora hemos examinado cómo convertir un desplazamiento en un ángulo. En esta sección entra la otra manera: toma un ángulo y lo convierte en X y los valores y. Algunos ejemplos comunes son un coche que se mueve en la dirección en que enfrenta o una nave espacial filmar una viñeta que se mueve en la dirección que es accesible desde el envío. 

Conceptualmente, la velocidad se puede calcular mediante la velocidad deseada cuando sin girado defina en primer lugar, a continuación, esa velocidad el ángulo que es accesible desde una entidad de la rotación. Para ayudar a explicar este concepto, se pueden visualizar velocity (y aceleración) como un 2 dimensiones *vector* (que es normalmente se dibuja como una flecha). Un vector para un valor de velocidad x = 100 e Y = 0 se puede visualizar como sigue:

![](math-images/image7.png "Un vector para un valor de velocidad x = 100 e Y = 0 se puede visualizar como se indica a continuación.")

Para dar lugar a una velocidad nuevo, se puede girar este vector. Por ejemplo, girar el vector de 45 grados (mediante el giro hacia la izquierda), se produce lo siguiente:

![](math-images/image8.png "Girar el vector de 45 grados mediante resultados de giro hacia la izquierda en este")

Afortunadamente, velocidad, la aceleración y la posición incluso todos se pueden girar con el mismo código, independientemente de cómo se aplican los valores. La siguiente función de uso general puede usarse para girar un vector por un valor de rotación de CocosSharp:


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

Un conocimiento completo de la `RotateVector` método requiere estar familiarizado con las funciones trigonométricas de coseno y seno junto con algunos álgebra lineal, que está fuera del ámbito de este artículo. Sin embargo, una vez implementado el `RotateVector` método puede utilizarse para girar cualquier vector, incluido un vector de velocidad. Por ejemplo, el siguiente código puede desencadenar una viñeta en una dirección especificada por el `rotation` valor:


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

Este código puede producir algo como:

![](math-images/image9.png "Este código puede producir algo parecido a esta captura de pantalla")


## <a name="summary"></a>Resumen

Esta guía trata conceptos matemáticos comunes en el desarrollo de juegos en 2D. Muestra cómo asignar e implementar la velocidad y la aceleración y se explica cómo girar objetos y vectores de movimiento en cualquier dirección.
