---
title: Cambios adicionales en iOS 10 marcos de trabajo
description: Este documento describe cambios menores y las mejoras realizadas en los marcos existentes en iOS 10 y se describe cómo hacer que el uso de estas actualizaciones en Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: a7b029aad69e65192d48d969dba2b9bb9a0d7a50
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233905"
---
# <a name="additional-ios-10-frameworks-changes"></a>Cambios adicionales en iOS 10 marcos de trabajo

_Este artículo tratan los cambios adicionales y menores o mejoras en los marcos existentes para iOS 10._

## <a name="av-foundation-framework-additions"></a>Adiciones de Framework AV Foundation

El marco de trabajo AVFoundation incluye las siguientes mejoras:

- En iOS 10, el desarrollador ya no tiene que implementar diferentes [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) comportamientos según el tipo de contenido. Basta con establecer la `Rate` propiedad y AVFoundation determinará cuándo suficiente contenido está disponible para su reproducción sin estancamiento.
- El nuevo [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) clase reemplaza al elemento desusado `AVCaptureStillImageOutput` clase y proporciona un método unificado para controlar todos los flujos de trabajo de la fotografía al proporcionar control sofisticada y la supervisión del proceso de captura y compatibilidad con nuevas características como Live fotos y el formato de captura sin procesar.
- El nuevo `AVPlayerLooper` clase facilita una parte determinada del medio de un bucle durante la reproducción.
- El `AVAssetDownloadURLSession` permite la descarga de la clase y su posterior reproducción de FairPlay cifra secuencias HLS.
- De forma predeterminada, el [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) clase admite automáticamente todo el color, la amplia gama de captura cuando el hardware del dispositivo lo admite. Consulte Apple [referencia de la compatibilidad de dispositivos iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) para obtener más detalles.

## <a name="avkit-additions"></a>Adiciones de AVKit

El marco de trabajo AVKit ahora incluye el nuevo `UpdatesNowPlayingInfoCenter` propiedad para indicar cuándo se debe actualizar el centro de información de juego ahora.

## <a name="core-data-enhancements"></a>Mejoras de datos principales

iOS 10 incluye las siguientes mejoras en el marco de trabajo de datos principales:

- El [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WAL la nueva generación de consultas de características donde se pueden anclar los contextos de objeto administrado (MOC) para versiones específicas de la base de datos para capturar futuras y transacciones con errores.
- Raíz [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) objetos admite produzca un error simultáneo y capturando sin serialización.
- El [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) clase mantiene un grupo de almacenes de datos de SQLite.
- Se han agregado varios métodos útiles de nuevo a `NSManagedObject` facilitando la tarea realizar búsquedas y crear subclases.
- Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) y otros recursos de configuración Core Data.

Para obtener más información, consulte Apple [referencia de Framework Core datos](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Mejoras de la imagen principal

iOS 10 realiza las siguientes mejoras en el marco de imagen Core:

- Ahora el desarrollador puede procesar imágenes en un espacio de colores fuera de espacio de color de trabajo del contexto de la imagen de Core convirtiendo dentro y fuera del espacio de colores antes y después del procesamiento.
- Para dispositivos iOS que usan el A8 o A9 CPU, ahora se admite el formato de imagen sin procesar. Imagen de Core ahora proporciona soporte técnico para descodificar imágenes sin formato desde la cámara integrada iSight o desde una cámara parte 3ª. Use la `FilterWithImageData` o `FilterWithImageURL` métodos de la [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) clase para procesar imágenes RAW.
- Se realizaron varias mejoras de rendimiento de representación en `UIImage` de representación (cuando está respaldado por almacenes de imagen de imagen Core) en `UIImageView` objetos. 
- `UIImage` los objetos etiquetados toda la gama representará como amplia gama de colores en `UIImageView` objetos en los dispositivos iOS que admiten color amplia.
- Código de kernel de imagen Core ahora puede solicitar los formatos de salida de píxel concreto.
- El `ImageWithExtent` método de la [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) clase puede usarse para insertar procesamiento personalizado en la operación de filtrado. Imagen de Core se invoque la devolución de llamada especificada entre los filtros al procesar una imagen para la salida o mostrar.

Además, se han agregado los siguientes filtros nuevos de imagen Core:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Adiciones de movimiento de Core

Nuevo IOS 10, el marco de movimiento de Core incluye eventos podómetro que permiten una aplicación rápida y reciba notificaciones en tiempo real del usuario pausar y reanudar el seguimiento mientras se está ejecutando. Use la [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) registrarse para eventos podómetro primer o segundo plano.

## <a name="foundation-enhancements"></a>Mejoras de Fundación

Se han realizado las siguientes mejoras para el marco de Foundation para iOS 10:

- Use la nueva [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) clase para dar formato localizadas medidas para mostrar al usuario final.
- Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar las intersecciones de intervalo.
- Use la nueva [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) clase para convertir entre distintas unidades de medida (UM) o realizar cálculos en los valores de UOMs diferentes.

- Use la nueva [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) las clases para representar UOMs específicos.
- Se han agregado nuevas propiedades a la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) clase adquirir información local y los formatos de visualización disponible.

## <a name="gamekit-enhancements"></a>Mejoras de GameKit

Se han realizado las siguientes mejoras para el marco de trabajo GameKit en iOS 10:

- El **Game Center aplicación** ha sido en desuso y quitados de iOS. Si la aplicación usa GameKit, lo _debe_ presentar su propia interfaz para mostrar las características de GameKit como marcadores, etcetera. 
- Se ha implementado un nuevo tipo de cuenta de iCloud solo por el [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) clase.
- El nuevo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) clase proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en Game Center. `GKGameSession` mantiene una lista de los jugadores y es responsable de implementar la aplicación cómo y cuándo fecha participante se almacenan, recuperan o intercambiado entre agentes. En muchos casos puede reemplazar a las sesiones de juego coincidencias existentes basada en turnos, coincidencias en tiempo real o juego persistente guardar métodos.

## <a name="gameplaykit-enhancements"></a>Mejoras de GameplayKit

Se han realizado las siguientes mejoras para el marco de trabajo GameplayKit en iOS 10:

- Use la nueva [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) clase para proporcionar rutas de acceso de alto rendimiento y aspecto natural.
- Generación de procedimientos de ruido se ha agregado y se puede usar para mejorar el realismo en texturas aspecto natural, aportar realismo a los movimientos de cámara y ayudar a generar los mundos de juegos enriquecidos.
- Utilice particiones espaciales para particionar los datos del mundo de juegos para la búsqueda eficaz.
- Estratega de Monte Carlo nuevo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) se ha agregado para el cálculo de movimiento exhaustiva de posibles.
- Se ha agregado compatibilidad con 3D al agente existente y los comportamientos de búsqueda de ruta con el nuevo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) clases.
- El nuevo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make clases combinación GameplayKit y SpriteKit más fácil que nunca.
- Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la inteligencia artificial de creación de juego.

## <a name="healthkit-enhancements"></a>Mejoras de HealthKit

Se han realizado las siguientes mejoras para el marco de trabajo HealthKit en iOS 10:

- Se han agregado nuevas claves de metadatos para los tipos de tiempo (como `HKWeatherConditionClear` y `HKWeatherConditionCloudy`) y los tipos de entrenamiento (como `HKWorkoutActivityTypeFlexibility` y `HKWorkoutActivityTypeWheelchairRunPace`) se han agregado.
- El nuevo `HKCDADocument` ha agregado la clase representar una arquitectura de documento clínico (CDA) con el formato de documento.
- Use la nueva [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) clase para especificar el `ActivityType` y `LocationType` de un entrenamiento.
- El nuevo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) y `WheelchairUse` método de la [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) se han agregado clases para trabajar con la silla de ruedas relacionados con los datos de estado.

## <a name="homekit-enhancements"></a>Mejoras de HomeKit

Se han realizado las siguientes mejoras para el marco de trabajo de HomeKit en iOS 10:

- Se agregaron nuevos servicios y características.
- Un iPad puede configurarse para que actúe como un concentrador de HomeKit para proporcionar acceso remoto de accesorio, ejecutar desencadenadores de automatización y habilitar compartido los permisos de usuario.
- Se agregó compatibilidad para la cámara y el timbre accesorios.
- Se han proporcionado más contexto y las configuraciones de accesorios.

Consulte nuestra [Introducción a HomeKit](~/ios/platform/homekit.md) documentación para obtener más información.

## <a name="metal-enhancements"></a>Mejoras de metal

Se han realizado las siguientes mejoras para el marco de trabajo completa en iOS 10:

- Ahora pueden usar los juegos y aplicaciones 3D _teselación_ eficazmente representar geometría a través de la GPU y escenas complejas.
- Proporcionar un mayor control de asignación de recursos para optimizar el rendimiento del sistema operativo basadas en aplicaciones con montones de recursos y destinos de representación Memoryless.
- Utilice la especialización de la función para crear una recopilación altamente optimizadas de material y funciones de la combinación de luz de una escena.

Para obtener más información, consulte Apple [Guía de programación de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Mejoras de ModelIO

Se han realizado las siguientes mejoras para el marco de trabajo ModelIO en iOS 10:

 - Ahora se admite el formato de archivo USD.
 - Firmado se ha agregado compatibilidad para el campo de distancia del [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) clase.
 - Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la selección de ubicación de sondeo de luz.
 - Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente en tiempo de ejecución cambia a los modelos.

## <a name="photos-enhancements"></a>Mejoras de fotos

Se han realizado las siguientes mejoras para el marco de fotos en iOS 10:

- Use la [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) y [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) clases para aprovechar las ventajas de la nueva característica de procesador de imagen básica para realizar modificaciones.
- Edición de fotos en vivo ahora está disponible para las aplicaciones que admiten el marco de fotos y a las extensiones de edición de fotos (para su uso dentro de las fotos y cámara aplicaciones).
- Use la nueva [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) clase aplicar ediciones a tanto el vídeo todavía contenido de fotos en vivo.

## <a name="replaykit-enhancements"></a>Mejoras de ReplayKit

Se han realizado las siguientes mejoras para el marco de trabajo ReplayKit en iOS 10:

- Use la [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) y [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) clases para admitir la difusión de registran el medio 3ª parte sitios.
- Las extensiones de interfaz de usuario de difusión y difusión cargar deben admitir los servicios de difusión de entidad ReplayKit 3rd en la aplicación.

## <a name="scenekit-enhancements"></a>Mejoras de SceneKit

Se han realizado las siguientes mejoras al marco de SceneKit de iOS 10:

- El [SCNCamera](xref:SceneKit.SCNCamera) clase puede proporcionar mayor realismo mediante el uso de características HDR y efectos. Use exposición adaptable para crear efectos automática o use las viñetas, halos de color y color de evaluación para agregar efectos fillmatic al juego.
- SceneKit ahora incluye un nuevo sistema físicamente en función de representación (PBR) para obtener resultados más realistas con la creación de activos más sencillo.
- Use la nueva [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreado del modelo al producto una amplia variedad de efectos de un sombreado realista al requerir que solo tres propiedades fundamentales (`Diffuse`, `Metalness` y `Roughness`).
- Desde PBR sombreado funciona mejor con la iluminación basado en el entorno, utilice el `LightingEnvironment` propiedad para asignar la iluminación basada en imágenes a una escena completa.
- Use el `IESProfileURL` propiedad para importar instalaciones fijas de luz del mundo real que definen la iluminación según los valores reales, como la intensidad (en lúmenes) y la temperatura de color (en grados Kelvin).
- Características de cámara PBR y HDR proporcionan mejores resultados que las técnicas tradicionales de representación y, como resultado, SceneKit realiza ahora todos los cálculos de color en un espacio de color lineal (con la gama de colores P3 en pantallas de dispositivo de todo el color).
- Color de SceneKit ahora coincide con todos los colores, lea la información de perfil de color.
- SceneKit interpreta los valores de componente de color en un espacio de colores RGB lineal para todos los tipos de sombreador.
- Tanto lineal como representación de espacio de color y todo el color se puede deshabilitar mediante la especificación de la `SCNDisableLinearSpaceRendering` y `SCNDisableWideGamut` claves en la aplicación `Info.plist`.
- Compilar los primates polígono arbitrario (bien cargarse desde un archivo o generar mediante programación) para especificar la geometría con el nuevo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) clase.
- Dado que SceneKit lee y ajustar para obtener información de perfil de color en imágenes de textura, utilice catálogos de recursos para todas las imágenes para asegurarse de que esta información se proporciona.

## <a name="spritekit-enhancements"></a>Mejoras de SpriteKit

Se han realizado las siguientes mejoras al marco de SpriteKit de iOS 10:

- Sombreadores personalizados pueden proporcionar atributos (`SKAttribute`) que puede configurarse por separado por cada nodo que utiliza el sombreador proporcionando un valor de atributo (`SKAttributeValue`).
- Tilemaps ahora son compatibles con las formas de mosaico cuadrado, hexagonal e isométrica para 2D, 2.5D y juegos de desplazamiento lateral con el `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` y `SKTileSet` clases.
- Use la nueva `SKWarpGeometry` clase estiramiento o distorsionar [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) o [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) representación. El nuevo [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) clase puede usarse para animar las transiciones entre los efectos de warp.
- El [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) clase proporciona varios métodos nuevos para darle un mayor control sobre cuándo y cómo se representa una escena.

## <a name="scrollview-enhancements"></a>Mejoras de ScrollView

Se han realizado las siguientes mejoras al control en iOS 10.3 ScrollView:

- `UIScrollView` ahora incluyen la `IndexDisplayMode` propiedad para controlar cómo se muestra el índice mientras se desplaza al usuario como un `UIScrollViewIndexDisplayMode` de:
    - `Automatic` -La presentación de índice se controla mediante el sistema operativo.
    - `AlwaysHidden` -La presentación de índice siempre está oculto.

Consulte la [iOSTenThree ejemplo](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) para su uso.

## <a name="uikit-enhancements"></a>Mejoras de UIKit

Se han realizado las siguientes mejoras para el marco UIKit en iOS 10:

- El nuevo [UIPasteboard](xref:UIKit.UIPasteboard) API ofrece nuevas opciones (por ejemplo, las limitaciones de la duración) y declarará automáticamente los tipos de contenido compatible para los tipos comunes de clase.
- Nueva compatibilidad de animación completamente interactivos, interrumpibles basado en el objeto se ha agregado y se puede vincular a los gestos. Consulte de Apple [referencia del protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [referencia de clase UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referencia del protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referencia de clase UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) y [referencia de clase UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obtener más información.
- El nuevo `UIPreviewInteraction` y `UIPreviewInteractionDelegate` permite que la aplicación de desarrollador proporcionar una interfaz personalizada para las operaciones de búsqueda peek y pop.
- El nuevo `UIAccessibilityCustomRotor` clase permite que la aplicación proporcionar funcionalidad personalizada, específicos del contexto a las tecnologías de asistencia como Voice Over.
- Use la `UIAccessibilityIsAssistiveTouchRunning` y `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar si está habilitado AssistiveTouch.
- Use la `UIAccessibilityHearingDevicePairedEar` y `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obtener el estado de cualquier emparejan MFi audífonos.
- Para admitir el tipo dinámico en las etiquetas, usan los nuevos campos de texto y cuadros de texto `PreferredFontForTextStyle` método de la `UIFont` clase.
- Para decidir si un elemento debe actualizar su fuente cuando el dispositivo `UIContentSizeCategory` cambios, utilice el `AdjustsFontForContentSizeCategory` propiedad de la `UIContentSizeCategoryAdjusting` delegar.
- El `OpenURL` método de la `UIApplication` clase se denomina de forma asincrónica y ahora es compatible con un controlador de finalización que se llama una vez completada la acción de apertura.
- Iniciar el uso compartido de CloudKit y modificar sus propiedades con el nuevo `UICloudSharingController` y `UICloudSharingControllerDelegate` clases.
- Aprovechar las ventajas de las celdas con captura previa para mejorar la experiencia de desplazamiento `UICollectionViews` con el nuevo `UICollectionViewDataSourcePrefetching` delegar.
- El desarrollador ahora puede controlar la apariencia de la notificación para elementos de la barra de ficha (por ejemplo, el color de texto y fondo).
- El Control actualice ahora se admite en todas las vista de desplazamiento y las subclases de la vista de desplazamiento (como `UICollectionView`).

## <a name="webkit-enhancements"></a>Mejoras de WebKit

Se han realizado las siguientes mejoras para el marco de WebKit en iOS 10:

- Peek y soporte técnico de confirmación se ha agregado a la `WKWebView` clase. Use el `ShouldPreviewElement` método para determinar si una vista web determinado debe mostrar una vista previa.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novedades de iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
