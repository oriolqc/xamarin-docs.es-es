---
title: SpriteKit en Xamarin.iOS
description: Este documento describe SpriteKit, el marco de Apple gráficos 2D que se integra con SceneKit, incorpora física y la animación, incluye compatibilidad para la iluminación y sombreado y mucho más. SpriteKit se puede usar para crear juegos en 2D.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: ef1e9a98b76166f4ee5638d1ab9762896d1e3bc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121651"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit en Xamarin.iOS

SpriteKit, el marco de gráficos 2D de Apple, tiene algunas nuevas características interesantes en iOS 8 y OS X Yosemite. Estos incluyen la integración con SceneKit, compatibilidad con el sombreador, iluminación, sombras, restricciones, generación de un mapa normal y mejoras de leyes físicas. En concreto, las nuevas características de leyes físicas facilitan agregar efectos realistas a un juego.

## <a name="physics-bodies"></a>Cuerpos de leyes físicas

SpriteKit incluye un 2D, física de un cuerpo rígido API. Cada sprite tiene un cuerpo de la física asociada (`SKPhysicsBody`) que define las propiedades físicas como masivo y fricción, así como la geometría del cuerpo en el mundo de leyes físicas.

## <a name="creating-a-physics-body-from-a-texture"></a>Creación de un cuerpo de leyes físicas de una textura
SpriteKit ahora admite derivar su textura en el cuerpo de la física de un sprite. Resulta fácil de implementar las colisiones con un aspecto más naturales.

Por ejemplo, tenga en cuenta en la siguiente colisión cómo el plátano y monkey entren en conflicto casi en la superficie de cada imagen:
 
![](spritekit-images/image13.png "El plátano y monkey entren en conflicto casi en la superficie de cada imagen.")

SpriteKit facilita la creación de un cuerpo de física de este tipo puede hacer con una sola línea de código. Basta con llamar a `SKPhysicsBody.Create` con el tamaño y la textura: sprite. PhysicsBody = SKPhysicsBody.Create (sprite. Textura, sprite. Tamaño);

## <a name="alpha-threshold"></a>Umbral alfa

Además de establecer simplemente la `PhysicsBody` propiedad directamente a la geometría que se deriva de la textura, las aplicaciones pueden establecer y umbral alfa para controlar cómo se deriva la geometría. 

El umbral alfa define el valor alfa mínimo que debe tener un píxel que se incluirán en el cuerpo de leyes físicas resultante. Por ejemplo, el código siguiente da como resultado un cuerpo de leyes físicas ligeramente diferente:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

El efecto de los ajustes del umbral alfa así el ajusta con precisión la colisión anterior, que pasa el objeto monkey al chocar con el plátano:

![](spritekit-images/image14.png "Pasa el objeto monkey al chocar con el plátano")
 
## <a name="physics-fields"></a>Campos de leyes físicas

Otra gran incorporación a SpriteKit es compatible con el nuevo campo de leyes físicas. Estos permiten agregar cosas como los campos vortex, campos de gravedad radial y campos de spring para nombrar solo unos pocos.

Campos de leyes físicas se crean mediante la clase SKFieldNode, que se agrega a una escena como cualquier otro `SKNode`. Hay una variedad de métodos de fábrica en `SKFieldNode` crear campos de leyes físicas diferentes. Puede crear un campo spring mediante una llamada a `SKFieldNode.CreateSpringField()`, mediante una llamada a un campo de gravedad radial `SKFieldNode.CreateRadialGravityField()`, y así sucesivamente.

`SKFieldNode` También tiene propiedades para controlar los atributos de campo, como la fuerza del campo, la región del campo y la atenuación de fuerza de campo.

## <a name="spring-field"></a>Campo de Spring

Por ejemplo, el código siguiente crea un campo de la primavera y lo agrega a la escena:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

A continuación, puede agregar sprites y establecer sus `PhysicsBody` propiedades para que el campo de leyes físicas afectará a los sprites, como hace el código siguiente cuando el usuario toca la pantalla:

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

Esto hace que el plátano oscilen como un muelle alrededor del nodo de campo:

![](spritekit-images/image15.png "Las bananas oscilen como un muelle alrededor del nodo de campo")
 
## <a name="radial-gravity-field"></a>Campo gravedad radial

Agregar un campo diferente es similar. Por ejemplo, el código siguiente crea un campo radial gravedad:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Esto da como resultado un campo diferente de fuerza, donde las bananas se extraen de forma radial sobre el campo:

![](spritekit-images/image16.png "Las bananas se extraen de forma radial alrededor del campo")
