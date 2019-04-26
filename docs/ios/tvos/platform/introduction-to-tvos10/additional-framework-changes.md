---
title: Cambios de marcos adicionales tvOS 10
description: Este documento describe cambios menores y las mejoras realizadas en los marcos existentes en iOS 10. Describe las actualizaciones de AVFoundation, AVKit, Core Data, Core Graphics, Foundation, GameKit, GameplayKit y mucho más.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 4ca9856251d35a741d496b6e8b45b07e851e0e89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932554"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Cambios de marcos adicionales tvOS 10

Además de los cambios más importantes para tvOS, Apple ha realizado modificaciones y mejoras para varios marcos de trabajo existentes en tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>Adiciones de AVFoundation Framework

El marco de trabajo AVFoundation incluye las siguientes mejoras:

 - En tvOS 10, la aplicación ya no se implementa en diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamientos según el tipo de contenido. Basta con establecer la `Rate` propiedad y AVFoundation determinará cuándo suficiente contenido está disponible para su reproducción sin estancamiento.
 - El nuevo `AVPlayerLooper` clase facilita una parte determinada del medio de un bucle durante la reproducción.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Mejoras de AVKit Framework

El marco de trabajo AVKit incluye las siguientes mejoras:

 - La aplicación ahora tiene control sobre el comportamiento de omitiendo la [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) por lo que podría mover un gesto omitiendo al elemento siguiente en la lista de reproducción o por adelantado dentro del elemento actual.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Mejoras de datos principales

tvOS 10 incluye las siguientes mejoras en el marco de trabajo de datos principales:

 - Raíz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos admite produzca un error simultáneo y capturando sin serialización.
 - El [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) clase mantiene un grupo de almacenes de datos de SQLite.
 - El [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WAL la nueva generación de consultas de características donde se pueden anclar los contextos de objeto administrado (MOC) para versiones específicas de la base de datos para capturar futuras y transacciones con errores.
 - Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración Core Data.
 - Se han agregado varios métodos útiles de nuevo a `NSManagedObject` facilitando la tarea realizar búsquedas y crear subclases.

Para obtener más información, consulte Apple [referencia de Framework Core datos](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Principales mejoras de gráficos

tvOS 10 incluye las siguientes mejoras en el marco de gráficos principal:

 - El nuevo [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) clase puede usarse para realizar una serie de conversiones de color.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Mejoras de la imagen principal

tvOS 10 realiza las siguientes mejoras en el marco de imagen Core:

 - El `ImageWithExtent` método de la [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) clase puede usarse para insertar procesamiento personalizado en la operación de filtrado. Imagen de Core se invoque la devolución de llamada especificada entre los filtros al procesar una imagen para la salida o mostrar.
 - La aplicación ahora puede procesar imágenes en un espacio de colores fuera de espacio de color de trabajo del contexto de la imagen de Core convirtiendo dentro y fuera del espacio de colores antes y después del procesamiento.
 - Se realizaron varias mejoras de rendimiento de representación en `UIImage` de representación (cuando está respaldado por almacenes de imagen de imagen Core) en `UIImageView` objetos. 
 - `UIImage` los objetos etiquetados toda la gama representará como amplia gama de colores en `UIImageView` objetos en los dispositivos iOS que admiten color amplia.
 - Código de kernel de imagen Core ahora puede solicitar los formatos de salida de píxel concreto.

Además, se han agregado los siguientes filtros nuevos de imagen Core:

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Mejoras de Fundación

El marco de trabajo Foundation para tvOS 10 se han realizado las siguientes mejoras:

 - Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar las intersecciones de intervalo.
 - Se han agregado nuevas propiedades a la [NSLocal](https://developer.apple.com/reference/foundation/nslocale) clase adquirir información local y los formatos de visualización disponible.
 - Use la nueva [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) clase para convertir entre distintas unidades de medida (UM) o realizar cálculos en los valores de UOMs diferentes.
 - Use la nueva [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) clase para dar formato localizadas medidas para mostrar al usuario final.
 - Use la nueva [NSUnit](https://developer.apple.com/reference/foundation/nsunit) y [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) las clases para representar UOMs específicos.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Mejoras de GameKit

Se han realizado las siguientes mejoras al marco de GameKit de tvOS 10:

 - Se ha implementado un nuevo tipo de cuenta de iCloud solo por el [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) clase.
 - El nuevo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) clase proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en Game Center. `GKGameSession` mantiene una lista de los jugadores y la aplicación es responsable de formulario implementar cómo y cuándo se almacena la fecha participante, puede recuperar o intercambiados entre agentes. En muchos casos puede reemplazar a las sesiones de juego coincidencias existentes basada en turnos, coincidencias en tiempo real o juego persistente guardar métodos.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Mejoras de GameplayKit

Se han realizado las siguientes mejoras al marco de GameplayKit de tvOS 10:

 - Generación de procedimientos de ruido se ha agregado y se puede usar para mejorar el realismo en texturas aspecto natural, aportar realismo a los movimientos de cámara y ayudar a generar los mundos de juegos enriquecidos.
 - Utilice particiones espaciales para particionar los datos del mundo de juegos para la búsqueda eficaz.
 - Estratega de Monte Carlo nuevo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) se ha agregado para el cálculo de movimiento exhaustiva de posibles.
 - Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la inteligencia artificial de creación de juego.
 - Se ha agregado compatibilidad con 3D al agente existente y los comportamientos de búsqueda de ruta con el nuevo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) clases.
 - Use la nueva [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) clase para proporcionar rutas de acceso de alto rendimiento y aspecto natural.
 - El nuevo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make clases combinación GameplayKit y SpriteKit más fácil que nunca.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Mejoras de metal

Se han realizado las siguientes mejoras al marco de Metal de tvOS 10:

 - Ahora pueden usar los juegos y aplicaciones 3D _teselación_ eficazmente representar geometría a través de la GPU y escenas complejas.
 - Utilice la especialización de la función para crear una recopilación altamente optimizadas de material y funciones de la combinación de luz de una escena.
 - Proporcionar un mayor control de asignación de recursos para optimizar el rendimiento del sistema operativo basadas en aplicaciones con montones de recursos y destinos de representación Memoryless.

Para obtener más información, consulte Apple [Guía de programación de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Mejoras de rendimiento metal sombreadores

Se han realizado las siguientes mejoras para el marco de trabajo de los sombreadores de rendimiento del sistema operativo en tvOS 10:

 - Muchos nuevos kernels se agregaron al marco de sombreadores de rendimiento del sistema operativo para permitir que la aplicación aprovechar los cálculos altamente optimizada, los datos en paralelo, como las conversiones de espacio de color y las operaciones de red neuronal.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Mejoras de ModelIO

Se han realizado las siguientes mejoras al marco de ModelIO de tvOS 10:

 - Ahora se admite el formato de archivo USD.
 - Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente en tiempo de ejecución cambia a los modelos.
 - Firmado se ha agregado compatibilidad para el campo de distancia del [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) clase.
 - Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la selección de ubicación de sondeo de luz.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Mejoras de SceneKit

Se han realizado las siguientes mejoras al marco de SceneKit de tvOS 10:

 - SceneKit ahora incluye un nuevo sistema físicamente en función de representación (PBR) para obtener resultados más realistas con la creación de activos más sencillo.
 - Use la nueva [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) sombreado del modelo al producto una amplia variedad de efectos de un sombreado realista al requerir que solo tres propiedades fundamentales (`Diffuse`, `Metalness` y `Roughness`).
 - Desde PBR sombreado funciona mejor con la iluminación basado en el entorno, utilice el `LightingEnvironment` propiedad para asignar la iluminación basada en imágenes en canela escena completa.
 - Use el `IESProfileURL` propiedad para importar instalaciones fijas de luz del mundo real que definen la iluminación base en los valores reales, como la intensidad (en lúmenes) y el color de temperatura (en grados Kelvin).
 - El [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) clase puede proporcionar mayor realismo mediante el uso de características HDR y efectos. Use exposición adaptable para crear efectos automática o use las viñetas, halos de color y color de evaluación para agregar efectos filmatic al juego.
 - Características de cámara PBR y HDR proporcionan mejores resultados que las técnicas tradicionales de representación y, como resultado, SceneKit realiza ahora todos los cálculos de color en un espacio de color lineal (con la gama de colores P3 en pantallas de dispositivo de todo el color).
 - Color de SceneKit ahora coincide con todos los colores, lea la información de perfil de color.
 - SceneKit interpreta los valores de componente de color en un espacio de colores RGB lineal para todos los tipos de sombreador.
 - Dado que SceneKit lee y ajustar para obtener información de perfil de color en imágenes de textura, utilice catálogos de recursos para todas las imágenes para asegurarse de que esta información se proporciona.
 - Tanto lineal como representación de espacio de color y todo el color se puede deshabilitar mediante la especificación de la `SCNDisableLinearSpaceRendering` y `SCNDisableWideGamut` claves en la aplicación `Info.plist`.
 - Compilar los primates polígono arbitrario (bien cargarse desde un archivo o generar mediante programación) para especificar la geometría con el nuevo [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) clase.

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Mejoras de SpriteKit

Se han realizado las siguientes mejoras al marco de SpriteKit de tvOS 10:

 - Tilemaps ahora son compatibles con las formas de mosaico cuadrado, hexagonal e isométrica para 2D, 2.5D y juegos de desplazamiento lateral con el `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` y `SKTileSet` clases.
 - Use la nueva `SKWarpGeometry` clase estiramiento o distorsionar [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) representación. El nuevo [SKAction](https://developer.apple.com/reference/spritekit/skaction) clase puede usarse para animar las transiciones entre los efectos de warp.
 - Sombreadores personalizados pueden proporcionar atributos (`SKAttribute`) que puede configurarse por separado por cada nodo que utiliza el sombreador proporcionando un valor de atributo (`SKAttributeValue`).
 - El [SKView](https://developer.apple.com/reference/spritekit/skview) clase proporciona varios métodos nuevos para darle un mayor control sobre cuándo y cómo se representa una escena.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Mejoras de UIKit

Se han realizado las siguientes mejoras para el marco UIKit en tvOS 10:

 - Se ha mejorado el foco de la API para compatibilidad con el foco del elemento de vista no además `UIViews`. Los elementos que admiten el foco _debe_ implementar el `IUIFocusItem` interfaz.
 - El nuevo `UIGraphicsRender` clase proporciona un método orientado a objetos de creación de mapas de bits o archivos PDF de representación de UIKit o gráficos esenciales y reemplaza el desuso `UIGraphicsBeginImageContext` método.
 - El `UIUserInterfaceStyle` se ha agregado la clase para determinar qué tema de la interfaz de usuario (oscuro o claro) está activo actualmente.
 - Se ha agregado nueva compatibilidad de animación completamente interactivos, basado en objetos, puede interrumpir y van vincularse a los gestos. Del Declinatoria vea Apple [referencia del protocolo UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [referencia de clase UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [referencia del protocolo UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Referencia de clase UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) y [referencia de clase UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) para obtener más información.
 - El nuevo `UIPreviewInteraction` y `UIPreviewInteractionDelegate` permite que la aplicación proporcionar una interfaz personalizada para las operaciones de búsqueda peek y pop.
 - El nuevo `UIAccessibilityCustomRotor` clase permite que la aplicación proporcionar funcionalidad personalizada, específicos del contexto a las tecnologías de asistencia como Voice Over.
 - Use la `UIAccessibilityIsAssistiveTouchRunning` y `UIAccessibilityAssistiveTouchStatusDidChangeNotification` símbolos para determinar si está habilitado AssistiveTouch.
 - Use la `UIAccessibilityHearingDevicePairedEar` y `UIAccessibilityHearingDevicePairedEarDidChangeNotification` símbolos para obtener el estado de cualquier emparejan MFi audífonos.
 - El nuevo [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API ofrece nuevas opciones (por ejemplo, las limitaciones de la duración) y declarará automáticamente los tipos de contenido compatible para los tipos comunes de clase.
 - Para admitir el tipo dinámico en las etiquetas, usan los nuevos campos de texto y cuadros de texto `PreferredFontForTextStyle` método de la `UIFont` clase.
 - Para decidir si un elemento deberá actualizarla fuente cuando los dispositivos `UIContentSizeCategory` cambios, utilice el `AdjustsFontForContentSizeCategory` propiedad de la `UIContentSizeCategoryAdjusting` delegar.
 - La aplicación ahora puede controlar la apariencia de la notificación para elementos de la barra de pestaña como el color de texto y fondo.
 - El Control actualice ahora se admiten en todas las vistas de desplazamiento y desplazamiento subclases (como `UICollectionView`).
 - El `OpenURL` método de la `UIApplication` clase se denomina ahora es compatible con un controlador de finalización que se llama una vez completada la apertura asincrónica.
 - Iniciar el uso compartido de CloudKit y modificar sus propiedades con el nuevo `UICloudSharingController` y `UICloudSharingControllerDelegate` clases.
 - Aprovechar las ventajas de las celdas con captura previa para mejorar la experiencia de desplazamiento `UICollectionViews` con el nuevo `UICollectionViewDataSourcePrefetching` delegar.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
