---
title: SceneKit en Xamarin.iOS
description: Este documento describe SceneKit, un gráfico de escena 3D API que simplifica el trabajo con gráficos 3D al abstraer las complejidades de OpenGL.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 0944978b34c8e164acd6e829db177bf4fd72dea9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109866"
---
# <a name="scenekit-in-xamarinios"></a>SceneKit en Xamarin.iOS

SceneKit es un gráfico de escena 3D API que simplifica el trabajo con gráficos 3D. Apareció por primera vez en OS X 10.8 y ha llegado a iOS 8. Con SceneKit crear visualizaciones 3D envolventes y juegos en 3D ocasionales no requiere experiencia en OpenGL. A partir de los conceptos comunes de gráfico de escena, SceneKit abstrae las complejidades de OpenGL y OpenGL ES, facilitando en gran medida 3D de agregar contenido a una aplicación. Sin embargo, si es un experto de OpenGL, SceneKit tiene mayor compatibilidad para enlazar directamente con OpenGL también. También incluye numerosas características que complementan los gráficos 3D, como física y se integra perfectamente con otros marcos de Apple, por ejemplo, Core Animation, Core imagen y Sprite Kit.

Es muy fácil de trabajar con SceneKit. Es una API declarativa que se encarga de la representación. Simplemente configure una escena, agregar propiedades a la base de datos y los identificadores de SceneKit la representación de la escena.

Para trabajar con SceneKit crear un gráfico de escena utilizando la `SCNScene` clase. Una escena contiene una jerarquía de nodos, representadas por instancias de `SCNNode`, definir las ubicaciones en el espacio 3D. Cada nodo tiene propiedades como geometry, iluminación y materiales que afectan a su apariencia, como se muestra en la ilustración siguiente:

![](scenekit-images/image7.png "La jerarquía de SceneKit") 

## <a name="create-a-scene"></a>Crear una escena

Para que una escena aparezca en pantalla, debe agregarse a un `SCNView` asignándola a la propiedad de la escena de la vista. Además, si realiza cambios en la escena, `SCNView` se actualizará para mostrar los cambios.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Segundo plano se puede rellenar de archivos que se exporta a través de una herramienta de modelado 3d, o mediante programación de primitivas geométricas. Por ejemplo, se trata de cómo crear una esfera y agregarlo a la escena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Adición de luz

En este momento la esfera no muestra nada porque no hay ninguna luz en la escena. Asociar `SCNLight` instancias a nodos crea las luces en SceneKit. Hay varios tipos de luces comprendido entre diversas formas de iluminación direccional iluminación ambiente. Por ejemplo, el código siguiente crea una luz multidireccionales en el lateral de la esfera:

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

Iluminación multidireccionales genera una reflexión difusa, lo que resulta en una iluminación incluso, algo como iluminar con una linterna. Crear luz ambiente es similar, aunque no tiene ninguna dirección tal como destaca por igual en todas las direcciones. Piense en ello como estado de ánimo iluminación :)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Con las luces en su lugar, la esfera ahora es visible en la escena.

![](scenekit-images/image8.png "La esfera está visible en la escena cuando iluminado")
 
## <a name="adding-a-camera"></a>Agregar una cámara

Agregar una cámara (SCNCamera) a la escena, cambia el punto de vista. El patrón para agregar la cámara es similar. Creación de la cámara, adjuntar a un nodo y agregue el nodo a la escena.

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

Como puede ver desde el código anterior, se pueden crear objetos mediante los constructores de SceneKit o desde el método Create factory. El primero permite usar C# sintaxis de inicializador, pero cuál usar es principalmente una cuestión de preferencia.

Con la cámara en su lugar, toda la esfera es visible para el usuario:

![](scenekit-images/image9.png "Toda la esfera es visible para el usuario")
 
Puede agregar más luces a la escena también. Este es su aspecto con unas luces multidireccionales más:

![](scenekit-images/image10.png "La esfera con unas luces multidireccionales más")
 
Además, estableciendo `sceneView.AllowsCameraControl = true`, el usuario puede cambiar el punto de vista con un gesto de toque.

### <a name="materials"></a>Materiales

Materiales se crean con la clase SCNMaterial. Por ejemplo agregar una imagen en la superficie de la esfera, Establece la imagen en el material *difuso* contenido.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Esta capa de la imagen en el nodo tal como se muestra a continuación:

![](scenekit-images/image11.png "La imagen en la esfera de la creación de capas")
 
Un material puede establecerse para responder a otros tipos de iluminación demasiado. Por ejemplo, el objeto se puede convertir brillante y ha establecido su contenido especular para mostrar de reflexión especular, lo que resulta en una zona brillante en la superficie, como se muestra a continuación:

![](scenekit-images/image12.png "El objeto que realiza brillante con reflexión especular, lo que resulta en una zona en la superficie brillante")
 
Materiales son muy flexibles, lo que permite lograr mucho con muy poco código. Por ejemplo, en lugar de establecer la imagen para el contenido de reflexión difuso, establézcalo como el contenido que se refleja en su lugar.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Ahora el objeto monkey aparece visualmente permanecer dentro de la esfera, independientemente de que el punto de vista.

### <a name="animation"></a>Animación

SceneKit está diseñado para funcionar bien con la animación. Puede crear animaciones implícitas o explícitas y aún puede representar una escena de un árbol de la capa de animación básica. Al crear una animación implícita, SceneKit proporciona su propia clase de transición, `SCNTransaction`.

Este es un ejemplo que gira la esfera:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

Puede animar aunque mucho más que la rotación. Muchas propiedades de SceneKit son que se pueden animar. Por ejemplo, el código siguiente anima el material `Shininess` para aumentar la reflexión especular.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit es muy fácil de usar. Ofrece una gran cantidad de características adicionales, como las restricciones, física, acciones declarativas, texto 3D, profundidad de soporte técnico de campo, la integración de Sprite Kit y la integración de imagen Core por nombrar solo unos pocos.