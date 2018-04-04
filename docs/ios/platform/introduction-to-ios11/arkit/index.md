---
title: Introducción a ARKit
description: Realidad aumentada para iOS 11
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: f48cdd48e63131fe234fef1bb60b555724dd8a92
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-arkit"></a>Introducción a ARKit

_Realidad aumentada para iOS 11_

ARKit permite una amplia variedad de juegos y aplicaciones de realidad aumentada. En esta sección se tratan los siguientes temas:

- [Introducción a ARKit](#gettingstarted)
- [Uso de ARKit con UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Introducción a ARKit

Para empezar a trabajar con la realidad aumentada, las siguientes instrucciones guiarán a través de una aplicación sencilla: colocar un modelo 3D y permitirle ARKit conservar el modelo en su lugar con su funcionalidad de seguimiento.

![Modelo 3D de Jet en cámara flotante imagen](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Agregar un modelo 3D

Activos deben agregarse al proyecto con el **SceneKitAsset** acción de compilación.

![SceneKit activos en un proyecto](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Configurar la vista

En el controlador de vista `ViewDidLoad` método, cargar el recurso de escena y establecer el `Scene` propiedad en la vista:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. Opcionalmente, puede implementar a un delegado de sesión

Aunque no es necesario para los casos más sencillos, implementar a un delegado de sesión puede ser útil para depurar el estado de la sesión de ARKit (y en las aplicaciones reales, proporcionar comentarios al usuario). Crear a un delegado simple mediante el código siguiente:

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

Asigne el delegado en el en el `ViewDidLoad` método:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Coloque el modelo 3D en el mundo

En `ViewWillAppear`, el código siguiente establece una sesión de ARKit y establece la posición del modelo 3D en el espacio relativo a la cámara del dispositivo:

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

Cada vez que se ejecuta la aplicación o se reanuda, se colocará el modelo 3D delante de la cámara. Una vez que se coloca el modelo, mover la cámara y observar cómo ARKit mantiene el modelo que se coloca.

### <a name="5-pause-the-augmented-reality-session"></a>5. Pausa la sesión realidad aumentada

Es recomendable para pausar la sesión de ARKit cuando el controlador de vista no está visible (en el `ViewWillDisappear` método:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Resumen

El código anterior da como resultado de una aplicación simple de ARKit. Ejemplos más complejos esperaría que hospeda la sesión realidad aumentada para implementar el controlador de vista `IARSCNViewDelegate`, y se implementan los métodos adicionales.

ARKit proporciona una gran cantidad de características más sofisticadas, como seguimiento expuesta y la interacción del usuario. Consulte la [UrhoSharp demostración](urhosharp.md) para obtener un ejemplo de combinación ARKit con UrhoSharp de seguimiento.


## <a name="related-links"></a>Vínculos relacionados

- [Realidad aumentada (Apple)](https://developer.apple.com/arkit/)
- [Uso de ARKit con UrhoSharp](urhosharp.md)
- [Ejemplo simple ARKit (Jet)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [Objetos de colocación de ARKit (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [Introducción a ARKit - realidad aumentada para iOS (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/602/)
