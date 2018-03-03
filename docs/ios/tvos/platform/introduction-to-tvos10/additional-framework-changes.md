---
title: Cambios de marcos de trabajo adicionales tvOS 10
description: "Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes tvos 10."
ms.topic: article
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 004abfffd3a100b7a25a9647fe233fd676f61143
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="additional-tvos-10-frameworks-changes"></a>Cambios de marcos de trabajo adicionales tvOS 10

_Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes tvos 10._


Además de los cambios más importantes a tvOS, Apple ha realizado modificaciones y mejoras para varios marcos de trabajo existentes en tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="av-foundation-framework-additions"></a>Adiciones de Framework de Foundation de AV

El marco de trabajo AVFoundation incluye las siguientes mejoras:

 - En tvOS 10, la aplicación ya no se implementa en diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamientos según el tipo de contenido. Basta con establecer la `Rate` propiedad y AVFoundation determinará cuándo suficiente contenido está disponible para su reproducción sin demora.
 - El nuevo `AVPlayerLooper` clase facilita una parte determinada del medio de un bucle durante la reproducción.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Mejoras de AVKit Framework

El marco de trabajo AVKit incluye las siguientes mejoras:

 - La aplicación ahora tiene control sobre el comportamiento de omitiendo la [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) por lo que un gesto omitiendo podría moverse al elemento siguiente en la lista de reproducción o pasar dentro del elemento actual.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Mejoras de datos principales

tvOS 10 incluye las siguientes mejoras en el marco de trabajo de datos principales:

 - Raíz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos es compatible con un error y capturar sin serialización simultáneas.
 - El [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) clase mantiene un grupo de almacenes de datos de SQLite.
 - El [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WLAN la nueva generación de consultas de características que se pueden anclar contextos de objeto administrado (MOC) a versiones específicas de la base de datos para capturar las futuras y transacciones con errores.
 - Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración de datos principal.
 - Varios métodos de comodidad nuevos se agregaron a `NSManagedObject` lo que sea más fácil realizar búsquedas y crear subclases.

Para obtener más información, vea de Apple [referencia de marco de trabajo de datos principal](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Mejoras de gráficos básica

tvOS 10 incluye las siguientes mejoras en el marco de trabajo de gráficos principal:

 - El nuevo [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) clase puede usarse para llevar a cabo una serie de conversiones de color.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Mejoras de la imagen principal

tvOS 10 realiza las siguientes mejoras en el marco de trabajo de la imagen principal:

 - El `ImageWithExtent` método de la [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) clase puede usarse para insertar procesamiento personalizado en la operación de filtrado. Imagen del núcleo se invoque la devolución de llamada especificado entre los filtros al procesar una imagen para la salida o mostrar.
 - La aplicación ahora puede procesar imágenes en un espacio de colores fuera del espacio de color de trabajo del contexto de la imagen principal mediante la conversión dentro y fuera del espacio de colores antes y después del procesamiento.
 - Se realizaron varias mejoras de rendimiento de representación en `UIImage` de representación (cuando respaldado por almacenes de imagen de imagen Core) en `UIImageView` objetos. 
 - `UIImage` objetos etiquetados amplia gama representará como amplia gama de colores en `UIImageView` objetos en dispositivos iOS que admiten color amplia.
 - Código de kernel de imagen básico ahora puede solicitar formatos de salida de píxel concreto.

Además, se han agregado los siguientes filtros de imágenes de núcleo nuevos:

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Mejoras de Fundación

Se han realizado las siguientes mejoras para el marco de trabajo de Foundation tvos 10:

 - Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar los cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar si hay intersecciones de intervalo.
 - Se agregaron nuevas propiedades a la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) clase adquirir información local y los formatos de presentación disponibles.
 - Use la nueva [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) clase para convertir entre diferentes unidades de medida (UM) o realizar cálculos en los valores de UOMs diferentes.
 - Use la nueva [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) clase para dar formato a medidas localizadas para mostrar al usuario final.
 - Use la nueva [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) las clases para representar UOMs específicos.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Mejoras de GameKit

Se han realizado las siguientes mejoras para el marco de trabajo de GameKit en tvOS 10:

 - Se ha implementado un nuevo tipo de cuenta solo iCloud la [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) clase.
 - El nuevo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) clase proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en el centro de juegos. `GKGameSession` mantiene una lista de jugadores y la aplicación es responsable forma implementar cómo y cuándo se almacena la fecha participante, recuperar o intercambiado reproductores. En muchos casos las sesiones de juego puede reemplazar coincidencias existentes basado en turnos, coincidencias en tiempo real o persistente juego guardado métodos.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Mejoras de GameplayKit

Se han realizado las siguientes mejoras para el marco de trabajo de GameplayKit en tvOS 10:

 - Generación de ruido de procedimientos se ha agregado y puede usarse para mejorar el realismo en texturas aspecto natural, agregar realismo a movimientos de cámara y ayudar a generar mundos de juegos enriquecidos.
 - Utilice particiones espaciales para particionar los datos de juegos para las búsquedas eficaces.
 - Un creador de estrategias de Monte Carlo nueva ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) se ha agregado para el cálculo de movimiento exhaustiva de posibles.
 - Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la AI de compilación de juego.
 - Se ha agregado compatibilidad con 3D al agente existente y comportamientos de búsqueda de la ruta de acceso con el nuevo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) clases.
 - Use la nueva [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) clase para proporcionar las rutas de acceso de alto rendimiento, aspecto natural.
 - El nuevo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) Asegúrese de que las clases combinación GameplayKit y SpriteKit más fácil que nunca.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Mejoras del sistema operativo

Se han realizado las siguientes mejoras a la plataforma de sistema operativo en tvOS 10:

 - Ahora pueden usar aplicaciones 3D y juegos _teselación_ eficazmente presentar escenas complejas y geometry a través de la GPU.
 - Utilice la especialización de la función para crear una colección de funciones de combinación claro para una escena y material más optimizado.
 - Proporcionar un control específico de asignación de recursos para optimizar el rendimiento del sistema operativo basadas en aplicaciones que usan recursos montones y los destinos de representación Memoryless.

Para obtener más información, vea de Apple [Guía de programación de sistema operativo](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Mejoras de sombreadores de rendimiento de sistema operativo

Se han realizado las siguientes mejoras para el marco de sombreadores de rendimiento del sistema operativo en tvOS 10:

 - Se agregaron muchas kernels nuevos para el marco de trabajo de sombreadores de rendimiento del sistema operativo para permitir que la aplicación aprovechar los cálculos altamente optimizada, datos en paralelo como las conversiones de espacio de color y las operaciones de red neuronal.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Mejoras de ModelIO

Se han realizado las siguientes mejoras para el marco de trabajo de ModelIO en tvOS 10:

 - Ahora se admite el formato de archivo USD.
 - Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente en tiempo de ejecución cambia a los modelos.
 - Firmado campo distancia que se ha agregado compatibilidad para la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) clase.
 - Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la ubicación de sondeo de luz.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Mejoras de SceneKit

Se han realizado las siguientes mejoras para el marco de trabajo de SceneKit en tvOS 10:

 - SceneKit ahora incluye un nuevo sistema físicamente en función de representación (PBR) para obtener resultados más realistas con creación más sencilla de activos.
 - Use la nueva [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreado modelos de producto una amplia gama de efectos de un sombreado realista al requerir que solo tres propiedades fundamentales (`Diffuse`, `Metalness` y `Roughness`).
 - Desde PBR sombreado funciona mejor con iluminación basada en el entorno, utilice el `LightingEnvironment` propiedad para asignar iluminación basada en imágenes en canela escena completa.
 - Use la `IESProfileURL` propiedad para importar los accesorios de iluminación reales que definen iluminación base en los valores reales como intensidad (en lúmenes) y el color de temperatura (en grados Kelvin).
 - El [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) clase puede proporcionar mayor realismo mediante características HDR y efectos. Use exposición adaptable para crear efectos automática o utilice las viñetas, halos de color y color de evaluación para agregar efectos filmatic para el juego.
 - Características de cámara PBR y HDR proporcionan mejores resultados que las técnicas tradicionales de representación y, como resultado, SceneKit realiza ahora todos los cálculos de color en un espacio de color lineal (con la gama de colores de P3 en pantallas de dispositivo de todo el color).
 - SceneKit ahora color coincide con todos los colores, lea la información de perfil de color.
 - SceneKit interpreta los valores de componente de color en un espacio de color RGB lineal para todos los tipos de sombreador.
 - Puesto que SceneKit lee y ajustar para obtener información de perfil de color de imágenes de textura, usar catálogos de activos para todas las imágenes para asegurarse de que esta información se proporciona.
 - Ambos lineal representación del espacio de color y todo el color se puede deshabilitar mediante la especificación de la `SCNDisableLinearSpaceRendering` y `SCNDisableWideGamut` claves en la aplicación `Info.plist`.
 - Compilar los primates polígono arbitrario (bien cargarse desde un archivo o generar mediante programación) para especificar la geometría con el nuevo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) clase.

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Mejoras de SpriteKit

Se han realizado las siguientes mejoras para el marco de trabajo de SpriteKit en tvOS 10:

 - Tilemaps ahora compatibilidad con mosaico cuadrado, hexagonal e isométrica formas 2D, 2,5 D y desplazamiento lateral juegos con la `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` y `SKTileSet` clases.
 - Use la nueva `SKWarpGeometry` clase estirar o distorsionar [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) representación. El nuevo [SKAction](https://developer.apple.com/reference/spritekit/skaction) clase puede usarse para animar transiciones entre los efectos de warp.
 - Sombreadores personalizados pueden proporcionar atributos (`SKAttribute`) que se puede configurar por separado cada nodo que utiliza el sombreador proporcionando un valor de atributo (`SKAttributeValue`).
 - El [SKView](https://developer.apple.com/reference/spritekit/skview) clase proporciona varios métodos nuevos para dar control más preciso sobre cuándo y cómo se representa una escena.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Mejoras de UIKit

Se han realizado las siguientes mejoras para el marco de trabajo de UIKit en tvOS 10:

 - El foco de la API se ha mejorado para permitir el foco del elemento de vista no además a `UIViews`. Elementos que admiten foco _debe_ implementar la `IUIFocusItem` interfaz.
 - El nuevo `UIGraphicsRender` clase proporciona un método de creación de mapas de bits o archivos PDF de representación UIKit o los gráficos de núcleo orientada a objetos y reemplaza las regiones `UIGraphicsBeginImageContext` método.
 - La `UIUserInterfaceStyle` clase se agregó para determinar qué tema de la interfaz de usuario (oscuro o claro) está activo actualmente.
 - Se ha agregado nueva compatibilidad de animación totalmente interactivo, basada en objetos, interrumpibles y van puede vincular en los gestos. Del Declinatoria vea Apple [referencia del protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [UIViewPropertyAnimator Class Reference](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referencia del protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referencia de clase UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) y [UISpringTimingParameter Class Reference](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obtener más información.
 - El nuevo `UIPreviewInteraction` y `UIPreviewInteractionDelegate` permite que la aplicación de la aplicación proporcionar una interfaz personalizada para las operaciones peek y pop.
 - El nuevo `UIAccessibilityCustomRotor` clase permite que la aplicación proporcionar funcionalidad personalizada, específicas del contexto para las tecnologías de asistencia como voz sobre.
 - Use la `UIAccessibilityIsAssistiveTouchRunning` y `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar si está habilitado AssistiveTouch.
 - Use la `UIAccessibilityHearingDevicePairedEar` y `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obtener el estado de cualquier emparejado MFi dispositivos de audición.
 - El nuevo [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API ofrece nuevas opciones (por ejemplo, las limitaciones de la duración) y declarará automáticamente los tipos de contenido compatible para los tipos de clase comunes.
 - Para admitir el tipo dinámico en las etiquetas, usan los nuevos campos de texto y cuadros de texto `PreferredFontForTextStyle` método de la `UIFont` clase.
 - Para decidir si un elemento deberá actualizarla fuente cuando los dispositivos `UIContentSizeCategory` cambios, use la `AdjustsFontForContentSizeCategory` propiedad de la `UIContentSizeCategoryAdjusting` delegar.
 - La aplicación ahora puede controlar la apariencia de la notificación para elementos de la barra de ficha, como el color de texto y fondo.
 - El Control actualice ahora se admiten en todas las vistas de desplazamiento y desplazamiento subclases (como `UICollectionView`).
 - El `OpenURL` método de la `UIApplication` clase se denomina ahora es compatible con un controlador de finalización que se llama una vez completada la apertura asincrónica.
 - Iniciar el uso compartido de CloudKit y modificar sus propiedades con el nuevo `UICloudSharingController` y `UICloudSharingControllerDelegate` clases.
 - Aprovechar las ventajas de las celdas precargadas para mejorar la experiencia de desplazamiento de `UICollectionViews` con la nueva `UICollectionViewDataSourcePrefetching` delegar.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
