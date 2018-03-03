---
title: MacOS adicionales Sierra Framework cambios
description: "Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes para macOS Sierra."
ms.topic: article
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: d49276d6367a52a05d486cd5cab198c666965bf7
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="additional-macos-sierra-framework-changes"></a>MacOS adicionales Sierra Framework cambios

_Este artículo trata sobre cambios adicionales, secundarios o mejoras en marcos existentes para macOS Sierra._

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Acelerar las mejoras de Framework

La siguiente mejora se realizaron en el marco de trabajo acelerar para macOS Sierra:

- En cuadratura agregada (cálculo entero).
- Agregado funciones básicas para construir redes neurales.
- Funciones de predicado geométricas agregadas para probar para cosas como la intersección de dos objetos geométricos.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Mejoras de Appkit Framework

La siguiente mejora se realizaron en el marco de trabajo AppKit para macOS Sierra:

- Varias mejoras en `NSCollectionView` como:
    - **Las secciones contraíbles** -permite al usuario contraer una sección de vista de colección en una sola fila horizontal.
    - **Flotante encabezados** -encabezados y pies de página ahora pueden flotar (en un diseño de flujo) con las mismas API que [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) en iOS.
    - **Vistas de fondo desplazable** -ahora se puede establecer un fondo de las vistas de colección para desplazar junto con el contenido.
- La fase de diseño de la vista diferida se ha optimizado y extendidos.
- La API de arrastrar y colocar ahora incluye la nueva `NSFilePromiseProvider` y `NSFilePromiseReceiver` clases para admitir arrastre flocado.
- Se han agregado varios constructores de comodidad para los controles existentes:
    -  `NSButton` incluye nuevos constructores para crear botones de comando, botones de radio y casillas de verificación.
    -  `NSTextField` incluye nuevos constructores para la creación de ajuste y no el ajuste de etiquetas, etiquetas con atributos y campos de texto editable.
    -  `NSSegmentedControl` incluye nuevos constructores para crear controles segmentados de un grupo de etiquetas o imágenes.
    -  `NSSlider` incluye nuevos constructores para crear controles deslizantes lineales horizontales.
    -  `NSImageView` incluye nuevos constructores para crear vistas de imagen no editables de un determinado `NSImage`.
-  El nuevo `NSGridView` se ha agregado a una colección de vistas de sub en una cuadrícula con la variable de tamaño de filas y columnas que se pueden ocultas o se muestra dinámicamente el Autodiseño.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Mejoras de AVFoundation Framework

La siguiente mejora se realizaron en el marco de trabajo AVFoundation para macOS Sierra:

- En Mac OS, la aplicación ya no tiene que implementar diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamientos según el tipo de contenido. Basta con establecer la `Rate` propiedad y AVFoundation determinará cuándo suficiente contenido está disponible para su reproducción sin demora.
- El nuevo `AVPlayerLooper` clase facilita una parte determinada del medio de un bucle durante la reproducción.
- La `AVAssetDownloadURLSession` clase permite la descarga y su posterior reproducción de FairPlay cifra secuencias HLS.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Mejoras de Framework de datos principales

La siguiente mejora se realizaron en el marco de trabajo de datos principal para macOS Sierra:

- Raíz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos es compatible con un error y capturar sin serialización simultáneas.
- El [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) clase mantiene un grupo de almacenes de datos de SQLite.
- El [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WLAN la nueva generación de consultas de características que se pueden anclar contextos de objeto administrado (MOC) a versiones específicas de la base de datos para capturar las futuras y transacciones con errores.
- Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración de datos principal.
- Varios métodos de comodidad nuevos se agregaron a `NSManagedObject` lo que sea más fácil realizar búsquedas y crear subclases.

Para obtener más información, vea de Apple [referencia de marco de trabajo de datos principal](https://developer.apple.com/reference/coredata).

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Mejoras de la imagen Framework Core

La siguiente mejora se realizaron en el marco de trabajo de imagen principal para macOS Sierra:

- El `ImageWithExtent` método de la [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) clase puede usarse para insertar procesamiento personalizado en la operación de filtrado. Imagen del núcleo se invoque la devolución de llamada especificado entre los filtros al procesar una imagen para la salida o mostrar.
- La aplicación ahora puede procesar imágenes en un espacio de colores fuera del espacio de color de trabajo del contexto de la imagen principal mediante la conversión dentro y fuera del espacio de colores antes y después del procesamiento.
- El núcleo de imagen Core ahora puede solicitar un formato de salida de píxel concreto.
- Se han agregado los siguientes filtros nuevos de imagen: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` y `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Mejoras de Framework de Foundation

La siguiente mejora se realizaron en el marco de trabajo de Foundation para macOS Sierra:

- Use la [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API para que representa, convertir y muchas de las unidades físicas más comunes, como se muestra en masa, longitud, la velocidad, la duración y la temperatura.
- Use la [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) fechas con el formato clase para su análisis y generar ISO 8601.
- Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar los cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar si hay intersecciones de intervalo.
- Use la [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) clase analizar los elementos de nombre de una persona de una cadena.
- Use la nueva [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) clase para obtener las métricas para una dirección URL de sesión de red.

Para obtener más información, vea de Apple [Foundation notas de la versión de iOS 10 y OS X v10.12](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Mejoras de GameKit Framework

La siguiente mejora se realizaron en el marco de trabajo GameKit para macOS Sierra:

- El **aplicación de centro de juego** en desuso y quitará macOS. Si la aplicación usa GameKit, lo _debe_ presentar su propia interfaz para mostrar las características de GameKit como tablas de líderes, etcetera. 
- Se ha implementado un nuevo tipo de cuenta solo iCloud la [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) clase.
- El nuevo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) clase proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en el centro de juegos. `GKGameSession` mantiene una lista de jugadores y la aplicación es responsable forma implementar cómo y cuándo se almacena la fecha participante, recuperar o intercambiado reproductores. En muchos casos las sesiones de juego puede reemplazar coincidencias existentes basado en turnos, coincidencias en tiempo real o persistente juego guardado métodos.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Mejoras de GamePlayKit Framework

La siguiente mejora se realizaron en el marco de trabajo GamePlayKit para macOS Sierra:

- Generación de ruido de procedimientos se ha agregado y puede usarse para mejorar el realismo en texturas aspecto natural, agregar realismo a movimientos de cámara y ayudar a generar mundos de juegos enriquecidos.
- Utilice particiones espaciales para particionar los datos de juegos para las búsquedas eficaces.
- Un creador de estrategias de Monte Carlo nueva ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) se ha agregado para el cálculo de movimiento exhaustiva de posibles.
- Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la AI de compilación de juego.
- Se ha agregado compatibilidad con 3D al agente existente y comportamientos de búsqueda de la ruta de acceso con el nuevo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) clases.
- Use la nueva [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) clase para proporcionar las rutas de acceso de alto rendimiento, aspecto natural.
- El nuevo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) Asegúrese de que las clases combinación GameplayKit y SpriteKit más fácil que nunca.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Mejoras de Framework completa

La siguiente mejora se realizaron en el marco de trabajo del sistema operativo para macOS Sierra:

- Ahora pueden usar aplicaciones 3D y juegos _teselación_ eficazmente presentar escenas complejas y geometry a través de la GPU.
- Utilice la especialización de la función para crear una colección de funciones de combinación claro para una escena y material más optimizado.
- Proporcionar un control específico de asignación de recursos para optimizar el rendimiento del sistema operativo basadas en aplicaciones que usan recursos montones y los destinos de representación Memoryless.

Para obtener más información, vea de Apple [Guía de programación de sistema operativo](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Mejoras en el marco de trabajo de E/S modelo

La siguiente mejora se realizaron en el marco de trabajo de E/S de modelo para macOS Sierra:

- Ahora se admite el formato de archivo USD.
- Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente en tiempo de ejecución cambia a los modelos.
- Firmado campo distancia que se ha agregado compatibilidad para la [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) clase.
- Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la ubicación de sondeo de luz.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Mejoras de marco de fotos

La siguiente mejora se realizaron en el marco de fotos para macOS Sierra:

- Edición de fotos en vivo ahora está disponible para las aplicaciones que admiten el marco de fotos y para las extensiones de edición fotográfica (para su uso dentro de las fotos y cámara aplicaciones).
- Use la nueva [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) clase aplicar cambios al vídeo y todavía contenido de fotos en vivo.
- Use la [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) y [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) clases para aprovechar las ventajas de la nueva característica de procesador de imagen Core para realizar ediciones.
- Para admitir fotos en vivo, el [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) y [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) clases se han trasladado de iOS a macOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Mejoras de SceneKit Framework

La siguiente mejora se realizaron en el marco de trabajo SceneKit para macOS Sierra:

- Ahora incluye un nuevo sistema físicamente en función de representación (PBR) para obtener resultados más realistas con creación más sencilla de activos.
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Mejoras de seguridad de Framework

La siguiente mejora se realizaron en el marco de seguridad para macOS Sierra:

- El `SecKey` interfaz se ha modernizado y unificada en todas las plataformas (iOS, tvOS, watchOS y macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Mejoras de SpriteKit Framework

La siguiente mejora se realizaron en el marco de trabajo SpriteKit para macOS Sierra:

- Tilemaps ahora compatibilidad con mosaico cuadrado, hexagonal e isométrica formas 2D, 2,5 D y desplazamiento lateral juegos con la `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` y `SKTileSet` clases.
- Use la nueva `SKWarpGeometry` clase estirar o distorsionar [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) representación. El nuevo [SKAction](https://developer.apple.com/reference/spritekit/skaction) clase puede usarse para animar transiciones entre los efectos de warp.
- Sombreadores personalizados pueden proporcionar atributos (`SKAttribute`) que se puede configurar por separado cada nodo que utiliza el sombreador proporcionando un valor de atributo (`SKAttributeValue`).
- El [SKView](https://developer.apple.com/reference/spritekit/skview) clase proporciona varios métodos nuevos para dar control más preciso sobre cuándo y cómo se representa una escena.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Marcos de trabajo de nuevo

Se han agregado los siguientes marcos para macOS Sierra:

- **Intentos Framework** -este marco de trabajo permite que la aplicación examinar las interacciones (por ejemplo, acciones de usuario o ubicación) y tomar medidas basadas en esa información.
- **SafariServices Framework** -este marco de trabajo permite que la aplicación desarrollar extensiones de aplicación para Safari (por ejemplo, los bloqueadores de elementos de contenido) para iOS y Mac OS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://developer.xamarin.com/samples/mac/)
- [' S new en OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
