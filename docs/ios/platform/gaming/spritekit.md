---
title: SpriteKit
ms.topic: article
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 79f7fbf52b544416223d225457d3148d68bc29e2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="spritekit"></a>SpriteKit

Kit de Sprite, el marco de juego 2D de Apple, tiene algunas nuevas características interesantes de iOS 8 y OS X Yosemite. Estos incluyen la integración con el Kit de escena, compatibilidad con el sombreador, iluminación, sombras, restricciones, la generación de asignación normal y mejoras de ciencias físicas. En particular, las nuevas características de ciencias físicas asegurarse muy fácil agregar efectos realistas a un juego.

## <a name="physics-bodies"></a>Cuerpos de ciencias físicas

Kit de Sprite incluye un 2D, física cuerpo rígida API. Cada sprite tiene un cuerpo asociado física (`SKPhysicsBody`) que define las propiedades de física como masivo y fricción, así como la geometría del cuerpo en el mundo físico.

## <a name="creating-a-physics-body-from-a-texture"></a>Crear un cuerpo física de una textura
Kit de Sprite ahora admite derivar el cuerpo de la física de un objeto sprite de su textura. Esto facilita el implementan conflictos que tenga un aspecto más naturales.

Por ejemplo, tenga en cuenta en la siguiente colisión cómo el plátano y mono entra en colisión casi en la superficie de cada imagen:
 
![](spritekit-images/image13.png "El plátano y mono entra en colisión casi en la superficie de cada imagen")

Kit de Sprite facilita la creación de un cuerpo de física de este tipo posible con una sola línea de código. Basta con llamar a `SKPhysicsBody.Create` con la textura y el tamaño: sprite. PhysicsBody = SKPhysicsBody.Create (sprite. Textura, sprite. Tamaño);

## <a name="alpha-threshold"></a>Umbral alfa

Además de establecer simplemente la `PhysicsBody` propiedad directamente a la geometría que se deriva de la textura, las aplicaciones pueden establecer y umbral alfa para controlar cómo se deriva la geometría. 

El umbral alfa define el valor alfa mínimo que debe tener un píxel que se incluirá en el cuerpo de ciencias físicas resultante. Por ejemplo, el código siguiente da como resultado un cuerpo física ligeramente diferente:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

El efecto de los ajustes del umbral alfa parecido a esto ajusta con precisión la colisión anterior, por ejemplo, que pasa el mono cuando entren con el plátano:

![](spritekit-images/image14.png "Pasa el mono cuando entren con el plátano")
 
## <a name="physics-fields"></a>Campos de ciencias físicas

Otra gran incorporación al Kit de Sprite es compatible con el nuevo campo de ciencias físicas. Estos le permiten agregar cosas como los campos de vortex, campos de gravedad radial y campos de primavera por nombrar solo unos pocos.

Campos de física se crean mediante la clase SKFieldNode, que se agrega a una escena como cualquier otro `SKNode`. Hay una variedad de métodos de generador en `SKFieldNode` para crear campos de ciencias físicas diferentes. Puede crear un campo de primavera mediante una llamada a `SKFieldNode.CreateSpringField()`, un campo de gravedad radial mediante una llamada a `SKFieldNode.CreateRadialGravityField()`, y así sucesivamente.

`SKFieldNode` También tiene propiedades que controlan los atributos de campo, como el nivel de campo y la región del campo, la atenuación de fuerza de campo.

## <a name="spring-field"></a>Campo de primavera

Por ejemplo, el código siguiente crea un campo de primavera y lo agrega a la escena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

A continuación, puede agregar objetos Sprite y establecer sus `PhysicsBody` propiedades para que el campo de ciencias físicas afectará los sprites, dado que el código siguiente se realiza cuando el usuario toca la pantalla:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

Esto hace que el plátano oscile como un resorte alrededor del nodo de campo:

![](spritekit-images/image15.png "El sector del plátano oscile como un resorte alrededor del nodo de campo")
 
## <a name="radial-gravity-field"></a>Campo de gravedad radial

Agregar un campo diferente es similar. Por ejemplo, el código siguiente crea un campo de gravedad radial:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

El resultado es un campo force diferente, donde se extrae forma radial el plátano sobre el campo:

![](spritekit-images/image16.png "El sector del plátano se extrae de forma radial alrededor del campo")