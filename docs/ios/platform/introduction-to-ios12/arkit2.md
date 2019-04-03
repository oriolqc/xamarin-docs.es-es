---
title: ARKit 2 en Xamarin.iOS
description: Este documento describe las actualizaciones ARKit en iOS 12. Se centra en usar objetos de referencia y las imágenes para la detección, incluye código para la textura del entorno y se describen los problemas comunes en la programación de ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/22/2018
ms.openlocfilehash: 559ef9cc9a3e5ace7a4023e81363825c6861f6d4
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870318"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 en Xamarin.iOS

ARKit ha madurado considerablemente desde su introducción del año pasado en iOS 11. En primer lugar y ante todo, puede detectar ahora vertical, así como los planos horizontales, lo cual mejora considerablemente la viabilidad de experiencias de realidad aumentada interiores. Además, hay nuevas funcionalidades:

* Reconocimiento de imágenes de referencia y los objetos como la unión entre el mundo real y las imágenes digitales
* Un nuevo modo de iluminación que simula la iluminación del mundo real
* La capacidad de compartir y conservar los entornos de cuentas por cobrar
* Un nuevo formato de archivo preferido para almacenar el contenido de cuentas por cobrar

## <a name="recognizing-reference-objects"></a>Reconocimiento de objetos de referencia

Una característica de presentación de ARKit 2 es la capacidad para reconocer imágenes de referencia y los objetos. Imágenes de referencia se pueden cargar desde los archivos de imagen normal ([describe más adelante](#more-tracking-configurations)), pero la referencia deben analizarse objetos mediante orientadas a los desarrolladores [ `ARObjectScanningConfiguration` ](xref:ARKit.ARObjectScanningConfiguration).

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>Aplicación de ejemplo: Análisis y detección de objetos 3D

El [examen y detectar objetos 3D](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) ejemplo es un puerto de un [proyecto Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) que muestra:

* Administración de estado de aplicación mediante [ `NSNotification` ](xref:Foundation.NSNotification) objetos
* Visualización personalizada
* Gestos complejos
* Objeto de examen
* Almacenar una [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

La detección de un objeto de referencia es el procesador y la batería y dispositivos más antiguos a menudo tendrán problemas para lograr un seguimiento estable.

### <a name="state-management-using-nsnotification-objects"></a>Administración de estados mediante objetos NSNotification

Esta aplicación usa una máquina de Estados que realiza la transición entre los estados siguientes:

* `AppState.StartARSession`
* `AppState.NotReady`
* `AppState.Scanning`
* `AppState.Testing`

Y además usa un conjunto de Estados incrustado y realiza la transición cuando se encuentra en `AppState.Scanning`:

* `Scan.ScanState.Ready`
* `Scan.ScanState.DefineBoundingBox`
* `Scan.ScanState.Scanning`
* `Scan.ScanState.AdjustingOrigin`

La aplicación utiliza una arquitectura reactiva que envía notificaciones de estado de transición a [ `NSNotificationCenter` ](xref:Foundation.NSNotificationCenter) y se suscribe a estas notificaciones. La configuración del aspecto de este fragmento de código `ViewController.cs`:

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

Un controlador de notificación típica suele actualizará la interfaz de usuario y, posiblemente, modificar el estado de la aplicación, por ejemplo, este controlador que se actualizan cuando se examina el objeto:

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

Por último, `Enter{State}` métodos modifican el modelo y la experiencia de usuario según corresponda para el nuevo estado:

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>Visualización personalizada

La aplicación se muestra el bajo nivel "punto" en la nube del objeto dentro de un cuadro de límite proyecta en un plano horizontal detectado.

En este punto, en la nube está disponible para los desarrolladores en el [ `ARFrame.RawFeaturePoints` ](xref:ARKit.ARFrame.RawFeaturePoints) propiedad. Visualización de la nube de punto de forma eficaz puede ser un problema peliagudo. Recorrer en iteración los puntos, a continuación, crear y colocar un nuevo nodo de SceneKit para cada punto de podrían terminar la velocidad de fotogramas. Como alternativa, si se realiza de forma asincrónica, habrá un retraso. El ejemplo mantiene el rendimiento con una estrategia de tres partes:

* Uso de código no seguro para anclar los datos de colocarán e interpretan los datos como un búfer de bytes sin formato.
* Convertir a ese búfer sin formato en un [ `SCNGeometrySource` ](xref:SceneKit.SCNGeometrySource) y la creación de una "plantilla" [ `SCNGeometryElement` ](xref:SceneKit.SCNGeometryElement) objeto.
* Rápidamente "unión" los datos sin procesar y la plantilla mediante [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

El resultado tendrá este aspecto:

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Gestos complejos

El usuario puede escalar, girar y arrastre el cuadro de límite que rodea el objeto de destino. Hay dos cosas interesantes en los reconocedores de gestos asociado.

En primer lugar, todos los reconocedores de gestos activan solo después de que se ha pasado un umbral; Por ejemplo, un dedo ha arrastrado tantas píxeles o la rotación supera algunos ángulo. La técnica consiste en el movimiento se acumulan hasta que se superó el umbral, a continuación, aplicarlo de forma incremental:

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

La segunda cosa interesante que se realiza en relación con los gestos es la manera en que se mueve el rectángulo de selección en relación con detectado planos del mundo real. Este aspecto se trata en [esta entrada de blog de Xamarin](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Otras características nuevas de ARKit 2

### <a name="more-tracking-configurations"></a>Más configuraciones de seguimiento

Ahora, puede usar cualquiera de las siguientes opciones como base para una experiencia de realidad mixta:

* Solo el acelerómetro del dispositivo ([`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration), iOS 11)
* Se enfrenta ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration), iOS 11)
* Haga referencia a imágenes ([`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration), 12 de iOS)
* Detección de objetos 3D ([`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration), 12 de iOS)
* Odometry inercial Visual ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration)mejorado en iOS 12)

`AROrientationTrackingConfiguration`, se describe en [esta entrada de blog y F# ejemplo](https://github.com/lobrien/FSharp_Face_AR), es más limitado y proporciona una experiencia de realidad mixta deficiente, como sólo coloca objetos digitales en relación con el movimiento del dispositivo, sin intentar asociar el dispositivo y en la pantalla el mundo real.

El `ARImageTrackingConfiguration` podrá reconocer imágenes en 2D reales (las pinturas, logotipos, etc.) y úselos para imágenes digitales delimitador:

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

Hay dos aspectos interesantes a esta configuración:

* Es eficaz y puede utilizarse con un número potencialmente grande de imágenes de referencia
* Las imágenes digitales está anclada a la imagen, incluso si se mueve esa imagen en el mundo real (por ejemplo, si se reconoce la tapa de un libro, realizará el seguimiento del libro que se saca de la estantería, colocan abajo, etcetera.).

El `ARObjectScanningConfiguration` se explicó [previamente](#recognizing-reference-objects) y es una configuración orientado a desarrolladores para la exploración de objetos 3D. Es algo muy procesador y uso intensivo de la batería y no debe usarse en aplicaciones de usuario final. El ejemplo [examen y detectar objetos 3D](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) muestra el uso de esta configuración.

La configuración de seguimiento final, `ARWorldTrackingConfiguration` , es el caballito de batalla de la mayoría de las experiencias de realidad mixta. Esta configuración usa "odometry inercial visual" para relacionar reales "puntos de función" en imágenes digitales. Geometría digital sprites anclados en relación con los planos verticales y horizontales reales o se relativo a detectado `ARReferenceObject` instancias. En esta configuración, el origen del mundo es la posición original de la cámara en el espacio con el eje z alineado con la gravedad y objetos digitales "permanecerán en el archivo" en relación con los objetos en el mundo real.

### <a name="environmental-texturing"></a>Textura del entorno

ARKit 2 es compatible con "medioambientales textura" que usa imágenes capturadas para calcular la iluminación e incluso aplicar resaltes especulares para objetos brillantes. El mapa de cubo del entorno se compila forma dinámica y, una vez que la cámara también ha buscado en todas las direcciones, puede generar una experiencia increíble realista:

![imagen de demostración de textura del entorno](images/arkit_env_texturing.png)

Para poder usar la textura del entorno:

* Su [ `SCNMaterial` ](xref:SceneKit.SCNMaterial) deben utilizar objetos [ `SCNLightingModel.PhysicallyBased` ](xref:SceneKit.SCNLightingModel.PhysicallyBased) y asignar un valor en el intervalo de 0 a 1 para [ `Metalness.Contents` ](xref:SceneKit.SCNMaterial.Metalness) y [ `Roughness.Contents` ](xref:SceneKit.SCNMaterialProperty.Contents) y
* Debe establecer la configuración de seguimiento [ `EnvironmentTexturing` ](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing)  =  [AREnvironmentTexturing.Automatic'](xref:ARKit.AREnvironmentTexturing.Automatic) :

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

Aunque la textura perfectamente reflectante, se muestra en el fragmento de código anterior es divertido en un ejemplo, la textura del entorno es preferible usar con retención para evitar que desencadene una respuesta "extraordinario valley" (la textura es sólo una estimación basada en lo que la cámara registrado).


### <a name="shared-and-persistent-ar-experiences"></a>Experiencias de AR compartidas y persistentes

Otra adición principal al ARKit 2 es el [ `ARWorldMap` ](xref:ARKit.ARWorldMap) (clase), que le permite compartir o almacenar datos de seguimiento del mundo. Obtener el mapa del mundo actual con [ `ARSession.GetCurrentWorldMapAsync` ](xref:ARKit.ARSession.GetCurrentWorldMapAsync) o [ `GetCurrentWorldMap(Action<ARWorldMap,NSError>` ](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

Para compartir o restaurar el mapa del mundo:

1. Cargar los datos desde el archivo,
2. Desarchivar en un `ARWorldMap` objeto,
3. Úsela como el valor de la [ `ARWorldTrackingConfiguration.InitialWorldMap` ](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) propiedad:

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

El `ARWorldMap` sólo contiene datos de seguimiento del mundo que no son visibles y [ `ARAnchor` ](xref:ARKit.ARAnchor) objetos, lo hace _no_ contienen los activos digitales. Para compartir imágenes o geometría, tendrá que desarrollar su propia estrategia adecuada para su caso de uso (quizás por almacenar o transmitir solo la ubicación y la orientación de la geometría y aplicarlo a estática `SCNGeometry` o quizás por almacenar o transmitir objetos serializados). La ventaja de la `ARWorldMap` es que los activos, una vez situados en relación con un compartido `ARAnchor`, aparecerá de forma coherente entre dispositivos o las sesiones.

### <a name="universal-scene-description-file-format"></a>Formato de archivo de descripción de la escena universal

La característica de final de los titulares de ARKit 2 es la adopción de Apple de Pixar [Universal Description escena](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) formato de archivo. Este formato reemplaza el formato DAE de Collada como el formato preferido para almacenar activos ARKit y compartir. Soporte técnico para visualizar los activos se integra en iOS 12 y Mojave. La extensión de archivo USDZ es un archivo zip cifrados y sin comprimir que contienen archivos USD. Pixar [proporciona herramientas para trabajar con archivos USD](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) pero aún no tiene soporte técnico de terceros mucho.

## <a name="arkit-programming-tips"></a>Sugerencias de programación de ARKit

### <a name="manual-resource-management"></a>Administración de recursos manual

En ARKit, es fundamental para administrar los recursos manualmente. No sólo permite velocidades de fotogramas alta, en realidad es _necesario_ para evitar un confuso "inmovilización de pantalla". El marco de ARKit es diferido sobre cómo proporcionar un nuevo marco de la cámara ([`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame). Hasta el actual [ `ARFrame` ](xref:ARKit.ARFrame) ha tenido `Dispose()` llamado en ella, ARKit no proporcionará un nuevo marco! Esto hace que el vídeo se "bloquee", aunque el resto de la aplicación está respondiendo. La solución consiste en acceder siempre `ARSession.CurrentFrame` con un `using` bloquear o llamar manualmente a `Dispose()` en él.

Todos los objetos derivados de `NSObject` son `IDisposable` y `NSObject` implementa el [patrón Dispose](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern), por lo que debe seguir normalmente [este modelo de implementación `Dispose` en una derivada clase](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>Manipulación de matrices de transformación

En cualquier aplicación 3D, va a estar tratando con matrices de transformación de 4 x 4 que describen cómo mover, girar y distorsión de un objeto a través de un espacio 3D de forma compacta. En SceneKit, son [ `SCNMatrix4` ](xref:SceneKit.SCNMatrix4) objetos.  

El [ `SCNNode.Transform` ](xref:SceneKit.SCNNode.Transform) propiedad devuelve el `SCNMatrix4` matriz de transformación para el [ `SCNNode` ](xref:SceneKit.SCNNode) _como respaldado por_ la fila principal `simdfloat4x4` tipo. Por lo tanto, por ejemplo:

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Como puede ver, la posición está codificada en los tres primeros elementos de la fila inferior.

En Xamarin, el tipo común para manipular matrices de transformación es `NVector4`, que por convención, se interpreta de forma columna principal. Es decir, se espera que el componente de traducción/position en M14, 24, M34, no M41, M42, M43:

![fila principal vs columna principal](images/arkit_row_vs_column.png)

Sea coherente con la opción de interpretación de la matriz es vital para el comportamiento apropiado. Puesto que las matrices de transformación 3D son 4 x 4, los errores de coherencia no producirá ningún tipo de excepción de tiempo de compilación o incluso en tiempo de ejecución, es simplemente operaciones actuará inesperadamente. Si su SceneKit / ARKit objetos parecen bloquearse, volar ausente o vibración, una matriz de transformación incorrecta es una buena posibilidad. La solución es sencilla: [ `NMatrix4.Transpose` ](xref:OpenTK.NMatrix4.Transpose*) llevará a cabo una transposición en lugar de elementos.

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo: análisis y detección de objetos 3D](https://developer.xamarin.com/samples/monotouch/iOS12/ScanningAndDetecting3DObjects/)
- [Novedades de ARKit 2 (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [ARKit descripción de seguimiento y la detección (sesión WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Introducción a ARKit en Xamarin.iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
