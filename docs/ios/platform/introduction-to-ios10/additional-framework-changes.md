---
title: Cambios de marcos de iOS adicional 10
description: "Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes para iOS 10."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: fef543291f0743f8ebfa799b67fca2c8243be9bc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="additional-ios-10-frameworks-changes"></a>Cambios de marcos de iOS adicional 10

_Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes para iOS 10._

## <a name="av-foundation-framework-additions"></a>Adiciones de Framework de Foundation de AV

El marco de trabajo AVFoundation incluye las siguientes mejoras:

- En iOS 10, el desarrollador ya no tiene que implementar diferentes [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) comportamientos según el tipo de contenido. Basta con establecer la `Rate` propiedad y AVFoundation determinará cuándo suficiente contenido está disponible para su reproducción sin demora.
- El nuevo [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) clase reemplaza las regiones `AVCaptureStillImageOutput` clase y proporciona un método unificado para controlar todos los flujos de trabajo de fotografía proporcionando sofisticado sistema de control y supervisión del proceso de captura y compatibilidad con características nuevas, como fotografías en vivo y el formato de captura sin formato.
- El nuevo `AVPlayerLooper` clase facilita una parte determinada del medio de un bucle durante la reproducción.
- La `AVAssetDownloadURLSession` clase permite la descarga y su posterior reproducción de FairPlay cifra secuencias HLS.
- De forma predeterminada, el [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) clase automáticamente admite captura toda la gama de colores, el color de todo el cuando el hardware del dispositivo lo admite. Vea de Apple [referencia de compatibilidad de dispositivos iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) para obtener más detalles.

## <a name="avkit-additions"></a>Adiciones de AVKit

El marco de trabajo AVKit incluye ahora el nuevo `UpdatesNowPlayingInfoCenter` propiedad para indicar cuándo se deben actualizar el centro de información de juego ahora.

## <a name="core-data-enhancements"></a>Mejoras de datos principales

iOS 10 incluye las siguientes mejoras en el marco de trabajo de datos principales:

- El [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WLAN la nueva generación de consultas de características que se pueden anclar contextos de objeto administrado (MOC) a versiones específicas de la base de datos para capturar las futuras y transacciones con errores.
- Raíz [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) objetos es compatible con un error y capturar sin serialización simultáneas.
- El [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) clase mantiene un grupo de almacenes de datos de SQLite.
- Varios métodos de comodidad nuevos se agregaron a `NSManagedObject` lo que sea más fácil realizar búsquedas y crear subclases.
- Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) y otros recursos de configuración de datos principal.

Para obtener más información, vea de Apple [referencia de marco de trabajo de datos principal](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Mejoras de la imagen principal

iOS 10 realiza las siguientes mejoras en el marco de trabajo de la imagen principal:

- El programador ahora puede procesar imágenes en un espacio de colores fuera del espacio de color de trabajo del contexto de la imagen principal mediante la conversión dentro y fuera del espacio de colores antes y después del procesamiento.
- Para dispositivos iOS que usan A8 o A9 CPU, ahora se admite el formato de imagen sin procesar. Imagen Core ahora ofrece soporte para la descodificación de imágenes sin formato desde la cámara iSight integrada o desde una cámara de entidad 3rd. Use la `FilterWithImageData` o `FilterWithImageURL` métodos de la [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) clase para procesar las imágenes RAW.
- Se realizaron varias mejoras de rendimiento de representación en `UIImage` de representación (cuando respaldado por almacenes de imagen de imagen Core) en `UIImageView` objetos. 
- `UIImage` objetos etiquetados amplia gama representará como amplia gama de colores en `UIImageView` objetos en dispositivos iOS que admiten color amplia.
- Código de kernel de imagen básico ahora puede solicitar formatos de salida de píxel concreto.
- El `ImageWithExtent` método de la [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) clase puede usarse para insertar procesamiento personalizado en la operación de filtrado. Imagen del núcleo se invoque la devolución de llamada especificado entre los filtros al procesar una imagen para la salida o mostrar.

Además, se han agregado los siguientes filtros de imágenes de núcleo nuevos:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Adiciones de movimiento de núcleo

Nuevo en iOS 10, el marco de trabajo de movimiento de Core incluye eventos de podómetro que habilita una aplicación rápida, recibir notificaciones en tiempo real del usuario pausar y reanudar el seguimiento mientras se está ejecutando. Use la [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) para registrar eventos de podómetro de primer plano o en segundo plano.

## <a name="foundation-enhancements"></a>Mejoras de Fundación

Se han realizado las siguientes mejoras para el marco de trabajo de Foundation para iOS 10:

- Use la nueva [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) clase para dar formato a medidas localizadas para mostrar al usuario final.
- Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar los cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar si hay intersecciones de intervalo.
- Use la nueva [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) clase para convertir entre diferentes unidades de medida (UM) o realizar cálculos en los valores de UOMs diferentes.

- Use la nueva [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) las clases para representar UOMs específicos.
- Se agregaron nuevas propiedades a la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) clase adquirir información local y los formatos de presentación disponibles.

## <a name="gamekit-enhancements"></a>Mejoras de GameKit

Se han realizado las siguientes mejoras para el marco de trabajo de GameKit en iOS 10:

- El **aplicación de centro de juego** en desuso y quitado de iOS. Si la aplicación usa GameKit, lo _debe_ presentar su propia interfaz para mostrar las características de GameKit como tablas de líderes, etcetera. 
- Se ha implementado un nuevo tipo de cuenta solo iCloud la [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) clase.
- El nuevo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) clase proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en el centro de juegos. `GKGameSession` mantiene una lista de reproductores y es responsable de implementar la aplicación cómo y cuándo fecha participante se almacenan, recuperar o intercambiado reproductores. En muchos casos las sesiones de juego puede reemplazar coincidencias existentes basado en turnos, coincidencias en tiempo real o persistente juego guardado métodos.

## <a name="gameplaykit-enhancements"></a>Mejoras de GameplayKit

Se han realizado las siguientes mejoras para el marco de trabajo de GameplayKit en iOS 10:

- Use la nueva [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) clase para proporcionar las rutas de acceso de alto rendimiento, aspecto natural.
- Generación de ruido de procedimientos se ha agregado y puede usarse para mejorar el realismo en texturas aspecto natural, agregar realismo a movimientos de cámara y ayudar a generar mundos de juegos enriquecidos.
- Utilice particiones espaciales para particionar los datos de juegos para las búsquedas eficaces.
- Un creador de estrategias de Monte Carlo nueva ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) se ha agregado para el cálculo de movimiento exhaustiva de posibles.
- Se ha agregado compatibilidad con 3D al agente existente y comportamientos de búsqueda de la ruta de acceso con el nuevo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) clases.
- El nuevo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) Asegúrese de que las clases combinación GameplayKit y SpriteKit más fácil que nunca.
- Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la AI de compilación de juego.

## <a name="healthkit-enhancements"></a>Mejoras de HealthKit

Se han realizado las siguientes mejoras para el marco de trabajo de HealthKit en iOS 10:

- Se han agregado nuevas claves de metadatos para tipos de tiempo (como `HKWeatherConditionClear` y `HKWeatherConditionCloudy`) y los tipos de entrenamiento (como `HKWorkoutActivityTypeFlexibility` y `HKWorkoutActivityTypeWheelchairRunPace`) se han agregado.
- El nuevo `HKCDADocument` ha agregado la clase para representar una arquitectura de documento clínicos (CDA) con el formato de documento.
- Use la nueva [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) clase para especificar el `ActivityType` y `LocationType` de un entrenamiento.
- El nuevo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) y `WheelchairUse` método de la [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) clase se han agregado para trabajar con silla de ruedas relacionadas con datos de estado.

## <a name="homekit-enhancements"></a>Mejoras de HomeKit

Se han realizado las siguientes mejoras para el marco de trabajo de HomeKit en iOS 10:

- Se agregaron características y servicios nuevos.
- Un iPad puede configurarse para que actúe como un concentrador de HomeKit para proporcionar acceso remoto de accesorio, ejecutar desencadenadores de automatización y habilitar compartidas permisos de usuario.
- Se ha agregado compatibilidad para accesorios de cámaras y timbre.
- Se han proporcionado más contexto y las configuraciones de accesorios.

Visite nuestro [Introducción a HomeKit](~/ios/platform/homekit.md) documentación para obtener más información.

## <a name="metal-enhancements"></a>Mejoras del sistema operativo

Se han realizado las siguientes mejoras a la plataforma de sistema operativo en iOS 10:

- Ahora pueden usar aplicaciones 3D y juegos _teselación_ eficazmente presentar escenas complejas y geometry a través de la GPU.
- Proporcionar un control específico de asignación de recursos para optimizar el rendimiento del sistema operativo basadas en aplicaciones que usan recursos montones y los destinos de representación Memoryless.
- Utilice la especialización de la función para crear una colección de funciones de combinación claro para una escena y material más optimizado.

Para obtener más información, vea de Apple [Guía de programación de sistema operativo](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Mejoras de ModelIO

Se han realizado las siguientes mejoras para el marco de trabajo de ModelIO en iOS 10:

 - Ahora se admite el formato de archivo USD.
 - Firmado campo distancia que se ha agregado compatibilidad para la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) clase.
 - Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la ubicación de sondeo de luz.
 - Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente en tiempo de ejecución cambia a los modelos.

## <a name="photos-enhancements"></a>Mejoras de fotos

Se han realizado las siguientes mejoras para el marco de fotos en iOS 10:

- Use la [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) y [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) clases para aprovechar las ventajas de la nueva característica de procesador de imagen Core para realizar ediciones.
- Edición de fotos en vivo ahora está disponible para las aplicaciones que admiten el marco de fotos y para las extensiones de edición fotográfica (para su uso dentro de las fotos y cámara aplicaciones).
- Use la nueva [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) clase aplicar cambios al vídeo y todavía contenido de fotos en vivo.

## <a name="replaykit-enhancements"></a>Mejoras de ReplayKit

Se han realizado las siguientes mejoras para el marco de trabajo de ReplayKit en iOS 10:

- Use la [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) y [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) clases que admiten la difusión de registran el medio en 3ª parte sitios.
- Las extensiones de interfaz de usuario de difusión y difusión cargar necesarios para admitir los servicios de difusión de entidad ReplayKit 3rd en la aplicación.

## <a name="scenekit-enhancements"></a>Mejoras de SceneKit

Se han realizado las siguientes mejoras para el marco de trabajo de SceneKit en iOS 10:

- El [SCNCamera](https://developer.xamarin.com/api/type/SceneKit.SCNCamera/) clase puede proporcionar mayor realismo mediante características HDR y efectos. Use exposición adaptable para crear efectos automática o utilice las viñetas, halos de color y color de evaluación para agregar efectos de fillmatic para el juego.
- SceneKit ahora incluye un nuevo sistema físicamente en función de representación (PBR) para obtener resultados más realistas con creación más sencilla de activos.
- Use la nueva [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreado modelos de producto una amplia gama de efectos de un sombreado realista al requerir que solo tres propiedades fundamentales (`Diffuse`, `Metalness` y `Roughness`).
- Desde PBR sombreado funciona mejor con iluminación basada en el entorno, utilice el `LightingEnvironment` propiedad para asignar iluminación basada en imágenes a toda una escena.
- Use la `IESProfileURL` propiedad para importar los accesorios de iluminación reales que definen la iluminación en función de valores reales como intensidad (en lúmenes) y la temperatura de color (en grados Kelvin).
- Características de cámara PBR y HDR proporcionan mejores resultados que las técnicas tradicionales de representación y, como resultado, SceneKit realiza ahora todos los cálculos de color en un espacio de color lineal (con la gama de colores de P3 en pantallas de dispositivo de todo el color).
- SceneKit ahora color coincide con todos los colores, lea la información de perfil de color.
- SceneKit interpreta los valores de componente de color en un espacio de color RGB lineal para todos los tipos de sombreador.
- Ambos lineal representación del espacio de color y todo el color se puede deshabilitar mediante la especificación de la `SCNDisableLinearSpaceRendering` y `SCNDisableWideGamut` claves en la aplicación `Info.plist`.
- Compilar los primates polígono arbitrario (bien cargarse desde un archivo o generar mediante programación) para especificar la geometría con el nuevo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) clase.
- Puesto que SceneKit lee y ajustar para obtener información de perfil de color de imágenes de textura, usar catálogos de activos para todas las imágenes para asegurarse de que esta información se proporciona.

## <a name="spritekit-enhancements"></a>Mejoras de SpriteKit

Se han realizado las siguientes mejoras para el marco de trabajo de SpriteKit en iOS 10:

- Sombreadores personalizados pueden proporcionar atributos (`SKAttribute`) que se puede configurar por separado cada nodo que utiliza el sombreador proporcionando un valor de atributo (`SKAttributeValue`).
- Tilemaps ahora compatibilidad con mosaico cuadrado, hexagonal e isométrica formas 2D, 2,5 D y desplazamiento lateral juegos con la `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` y `SKTileSet` clases.
- Use la nueva `SKWarpGeometry` clase estirar o distorsionar [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) o [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) representación. El nuevo [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) clase puede usarse para animar transiciones entre los efectos de warp.
- El [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) clase proporciona varios métodos nuevos para dar control más preciso sobre cuándo y cómo se representa una escena.

## <a name="scrollview-enhancements"></a>Mejoras de ScrollView

Se realizaron las siguientes mejoras en el control ScrollView en iOS 10.3:

- `UIScrollView` ahora incluyen la `IndexDisplayMode` propiedad para controlar cómo se muestra el índice mientras se desplaza el usuario como un `UIScrollViewIndexDisplayMode` de:
    - `Automatic` -La presentación de índice se controla por el sistema operativo.
    - `AlwaysHidden` -La presentación de índice siempre está oculto.

Consulte la [iOSTenThree ejemplo](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) para su uso.

## <a name="uikit-enhancements"></a>Mejoras de UIKit

Se han realizado las siguientes mejoras para el marco de trabajo de UIKit en iOS 10:

- El nuevo [UIPasteboard](https://developer.xamarin.com/api/type/UIKit.UIPasteboard/) API ofrece nuevas opciones (por ejemplo, las limitaciones de la duración) y declarará automáticamente los tipos de contenido compatible para los tipos de clase comunes.
- Nueva compatibilidad de animación totalmente interactivo, interrumpibles basado en el objeto se ha agregado y se puede vincular en los gestos. Del Declinatoria vea Apple [referencia del protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [UIViewPropertyAnimator Class Reference](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referencia del protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referencia de clase UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) y [UISpringTimingParameter Class Reference](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obtener más información.
- El nuevo `UIPreviewInteraction` y `UIPreviewInteractionDelegate` permite que la aplicación de desarrollador proporcionar una interfaz personalizada para las operaciones peek y pop.
- El nuevo `UIAccessibilityCustomRotor` clase permite que la aplicación proporcionar funcionalidad personalizada, específicas del contexto para las tecnologías de asistencia como voz sobre.
- Use la `UIAccessibilityIsAssistiveTouchRunning` y `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar si está habilitado AssistiveTouch.
- Use la `UIAccessibilityHearingDevicePairedEar` y `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obtener el estado de cualquier emparejado MFi dispositivos de audición.
- Para admitir el tipo dinámico en las etiquetas, usan los nuevos campos de texto y cuadros de texto `PreferredFontForTextStyle` método de la `UIFont` clase.
- Para decidir si un elemento debe actualizar su fuente cuando el dispositivo `UIContentSizeCategory` cambios, use la `AdjustsFontForContentSizeCategory` propiedad de la `UIContentSizeCategoryAdjusting` delegar.
- El `OpenURL` método de la `UIApplication` clase se llama de forma asincrónica y ahora es compatible con un controlador de finalización que se llama una vez completada la acción open.
- Iniciar el uso compartido de CloudKit y modificar sus propiedades con el nuevo `UICloudSharingController` y `UICloudSharingControllerDelegate` clases.
- Aprovechar las ventajas de las celdas precargadas para mejorar la experiencia de desplazamiento de `UICollectionViews` con la nueva `UICollectionViewDataSourcePrefetching` delegar.
- El programador ahora puede controlar la apariencia de la notificación para elementos de la barra de ficha (por ejemplo, color de texto y de fondo).
- El Control actualice ahora se admite en todas las vistas de desplazamiento y subclases de la vista de desplazamiento (como `UICollectionView`).

## <a name="webkit-enhancements"></a>Mejoras de WebKit

Se han realizado las siguientes mejoras para el marco de WebKit en iOS 10:

- Información y soporte técnico de confirmación se ha agregado a la `WKWebView` clase. Use la `ShouldPreviewElement` método para determinar si una vista web determinado debe mostrar una vista previa.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novedades de iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
