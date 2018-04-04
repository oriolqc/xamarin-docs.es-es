---
title: SceneKit
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 7c00a3f6aed442eec402f34a5cea4b1895bb3685
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="scenekit"></a>SceneKit

Kit de escena es un gráfico de escena 3D API que simplifica el trabajo con gráficos 3D. Apareció por primera vez en OS X 10.8 y ha llegado a iOS 8. Con el Kit de escena crear visualizaciones 3D envolventes y juegos 3D ocasionales no requiere tener conocimientos de OpenGL. Basándose en los conceptos de gráfico de escena comunes, Kit de escena abstrae las complejidades de OpenGL y OpenGL ES, facilitando muy 3D de agregar contenido a una aplicación. Sin embargo, si es un experto de OpenGL, Kit de escena tiene mayor compatibilidad para enlazar directamente con OpenGL así. También incluye varias características que complementan a los gráficos 3D, como física y se integra perfectamente con otros marcos de Apple, como animación de núcleo, imagen Core y Sprite Kit.

Kit de escena es una tarea muy sencilla para que funcione con. Es una API declarativa que se encarga de la representación. Basta con configurar una escena, agregar propiedades a la base de datos y Kit de escena controla la representación de la escena.

Para trabajar con el Kit de escena que se crea un gráfico de escena utilizando la `SCNScene` clase. Una escena contiene una jerarquía de nodos, representados por instancias de `SCNNode`, definir ubicaciones en un espacio 3D. Cada nodo tiene propiedades, como la geometría, iluminación y materiales que afectan a su apariencia, tal y como se muestra en la ilustración siguiente:

![](scenekit-images/image7.png "La jerarquía SceneKit") 

## <a name="create-a-scene"></a>Crear una escena

Para realizar una escena aparecen en pantalla, debe agregarse a un `SCNView` mediante la asignación a la propiedad vista de escena. Además, si realiza cambios en la escena `SCNView` se actualizará para mostrar los cambios.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Plano se puede rellenar desde archivos exportados a través de una herramienta de modelado 3d, o mediante programación desde primitivas geométricas. Por ejemplo, se trata de cómo crear una esfera y agregarlo a la escena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Adición de luz

En este momento la esfera no muestra nada porque no hay ninguna luz de la escena. Adjuntar `SCNLight` instancias de nodos crea luces en escena Kit. Hay varios tipos de indicadores luminosos comprendido entre diversas formas de iluminación direccional y luz ambiental. Por ejemplo, el código siguiente crea una luz omnidireccional en el lateral de la esfera:

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

Iluminación omnidireccional genera una reflexión difusa resultante en una iluminación incluso, tipo de como iluminar con una linterna. Creación de la luz ambiental es similar, aunque no tiene ninguna dirección tal y como destaca es igual en todas las direcciones. Considerar como ambiente iluminación :)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Con las luces en su lugar, la esfera ahora está visible en la escena.

![](scenekit-images/image8.png "La esfera está visible en la escena cuando iluminado")
 
## <a name="adding-a-camera"></a>Adición de una cámara

Agregar una cámara (SCNCamera) a la escena cambia el punto de vista. El patrón para agregar la cámara es similar. Crear la cámara, asociar a un nodo y agregar el nodo a la escena.

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

Como puede ver desde el código anterior, Kit de escena se pueden crear objetos mediante constructores o desde el método de fábrica de Create. Permite que el primero usa la sintaxis de inicializador de C#, pero cuál utilizar es en gran medida una cuestión de preferencia.

Con la cámara en su lugar, toda la esfera está visible para el usuario:

![](scenekit-images/image9.png "Toda la esfera está visible para el usuario")
 
Puede agregar las luces adicionales a la escena así. Este es su aspecto con unas luces omnidireccional más:

![](scenekit-images/image10.png "La esfera con unas luces omnidireccional más")
 
Además, estableciendo `sceneView.AllowsCameraControl = true`, el usuario puede cambiar el punto de vista con un gesto de entrada táctil.

### <a name="materials"></a>Materiales

Materiales se crean con la clase SCNMaterial. Por ejemplo agregar una imagen a la superficie de la esfera, establecer la imagen para el material *dispersan* contenido.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Esta capa de la imagen en el nodo tal y como se muestra a continuación:

![](scenekit-images/image11.png "La imagen en la esfera disponer en capas")
 
Puede establecerse un material para responder a otros tipos de iluminación demasiado. Por ejemplo, el objeto se puede convertir brillante y ha configurado su contenido especular para mostrar reflexión especular, lo que produce un punto luminoso en la superficie, tal y como se muestra a continuación:

![](scenekit-images/image12.png "El objeto realizado brillante con reflexión especular, lo que da lugar a un punto luminoso en la superficie")
 
Materiales son muy flexibles, lo que permite lograr mucho con muy poco código. Por ejemplo, en lugar de establecer la imagen para el contenido de difusión, establézcala en el contenido brilla en su lugar.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Ahora el mono parece visualmente se colocan dentro de la esfera, independientemente de que el punto de vista.

### <a name="animation"></a>Animación

Kit de escena está diseñado para funcionar correctamente con la animación. Puede crear animaciones implícitas o explícitas e incluso se puede representar una escena de un árbol de la capa de animación de núcleo. Al crear una animación implícita, Kit de escena proporciona su propia clase de transición, `SCNTransaction`.

Este es un ejemplo que gira la esfera:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

Puede animar aunque mucho más que la rotación. Muchas propiedades de escena Kit son pueden animar. Por ejemplo, el código siguiente anima el material `Shininess` para aumentar la reflexión especular.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

Kit de escena es muy fácil de usar. Ofrece una gran variedad de características adicionales tales como las restricciones, física, acciones declarativa, texto 3D, profundidad de soporte técnico de campo, la integración de Sprite Kit y la integración de imagen Core por nombrar solo unos pocos.