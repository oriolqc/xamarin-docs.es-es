---
title: Adicionales macOS Sierra cambios en la plataforma
description: Este documento describe las mejoras y cambios menores para marcos existentes que se introdujo en macOS Sierra. Examina los cambios realizados en el marco de trabajo Acelere, AppKit, AVFoundation, Core Data, imagen básica, Foundation y mucho más.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6ed827c018931e5b79887dc355f136e2a84623d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111056"
---
# <a name="additional-macos-sierra-framework-changes"></a>Adicionales macOS Sierra cambios en la plataforma

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Acelere las mejoras de Framework

Se han realizado al marco para macOS Sierra acelerar la siguiente mejora:

- En cuadratura agregada (integral de cálculo).
- Se ha agregado las funciones básicas para construir las redes neuronales.
- Funciones de predicado geométricas agregadas para probar cosas como la intersección de dos objetos geométricos.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Mejoras de AppKit Framework

La siguiente mejora se realizaron en el marco de trabajo de AppKit para macOS Sierra:

- Diversas mejoras al `NSCollectionView` como:
    - **Las secciones contraíbles** -permite al usuario contraer una sección de vista de colección en una sola fila horizontal.
    - **Flotante encabezados** -encabezados y pies de página ahora pueden flotar (en un diseño de flujo) con las mismas API como [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) en iOS.
    - **Vistas de fondo desplazable** -ahora se puede establecer un fondo de las vistas de colección para desplazar junto con el contenido.
- La fase de diseño de la vista diferida se ha optimizado y extendidos.
- La API de arrastrar y colocar ahora incluye el nuevo `NSFilePromiseProvider` y `NSFilePromiseReceiver` clases para admitir arrastre aves.
- Se han agregado varios constructores de comodidad a los controles existentes:
    -  `NSButton` incluye nuevos constructores para crear botones de comando, los botones de radio y casillas de verificación.
    -  `NSTextField` incluye nuevos constructores para crear las etiquetas de ajuste y que no son de ajuste, con atributos de las etiquetas y campos de texto editable.
    -  `NSSegmentedControl` incluye nuevos constructores para crear controles segmentados de un grupo de imágenes o etiquetas.
    -  `NSSlider` incluye nuevos constructores para crear los controles deslizantes de lineales horizontales.
    -  `NSImageView` incluye nuevos constructores para crear vistas de la imagen no se puede modificar desde un determinado `NSImage`.
-  El nuevo `NSGridView` se ha agregado una colección de vistas de sub en una cuadrícula con la variable de tamaño de filas y columnas que se pueden ocultas dinámicamente o que se muestra el diseño auto.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Mejoras de AVFoundation Framework

La siguiente mejora se realizaron en el marco de trabajo AVFoundation para macOS Sierra:

- En macOS, la aplicación ya no tiene que implementar diferentes [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) comportamientos según el tipo de contenido. Basta con establecer la `Rate` propiedad y AVFoundation determinará cuándo suficiente contenido está disponible para su reproducción sin estancamiento.
- El nuevo `AVPlayerLooper` clase facilita una parte determinada del medio de un bucle durante la reproducción.
- El `AVAssetDownloadURLSession` permite la descarga de la clase y su posterior reproducción de FairPlay cifra secuencias HLS.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Mejoras de marco de trabajo de datos principales

La siguiente mejora se realizaron en el marco de datos principal para macOS Sierra:

- Raíz [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos admite produzca un error simultáneo y capturando sin serialización.
- El [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) clase mantiene un grupo de almacenes de datos de SQLite.
- El [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) objetos con almacenes de datos de SQLite en la compatibilidad con el modo de diario de WAL la nueva generación de consultas de características donde se pueden anclar los contextos de objeto administrado (MOC) para versiones específicas de la base de datos para capturar futuras y transacciones con errores.
- Con el alto nivel `NSPersistenceContainer` para hacer referencia a la `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) y otros recursos de configuración Core Data.
- Se han agregado varios métodos útiles de nuevo a `NSManagedObject` facilitando la tarea realizar búsquedas y crear subclases.

Para obtener más información, consulte Apple [referencia de Framework Core datos](https://developer.apple.com/reference/coredata).

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Mejoras de la imagen Framework Core

La siguiente mejora se realizaron en el marco de la imagen principal para macOS Sierra:

- El `ImageWithExtent` método de la [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) clase puede usarse para insertar procesamiento personalizado en la operación de filtrado. Imagen de Core se invoque la devolución de llamada especificada entre los filtros al procesar una imagen para la salida o mostrar.
- La aplicación ahora puede procesar imágenes en un espacio de colores fuera de espacio de color de trabajo del contexto de la imagen de Core convirtiendo dentro y fuera del espacio de colores antes y después del procesamiento.
- El núcleo de la imagen de Core ahora puede solicitar un formato de salida de píxel concreto.
- Se han agregado los siguientes filtros nuevos de imagen: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` y `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Mejoras de Framework Foundation

Se han realizado la siguiente mejora el marco de trabajo para macOS Sierra Foundation:

- Use la [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API para que representa, convirtiendo y mostrar muchas de las unidades físicas más comunes, como masa, longitud, velocidad, duración y la temperatura.
- Use la [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) clase para analizar y generar ISO 8601 con formato de fechas.
- Use la nueva [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) clase para realizar cálculos de intervalo de fecha y hora como duraciones, de comparación de intervalos y probar las intersecciones de intervalo.
- Use la [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) clase para analizar los elementos de nombre de una persona de una cadena.
- Use la nueva [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) clase para obtener las métricas para una dirección URL de sesión de red.

Para obtener más información, consulte Apple [Foundation notas de la versión para iOS 10 y OS X v10.12](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Mejoras de GameKit Framework

La siguiente mejora se realizaron en el marco de trabajo GameKit para macOS Sierra:

- El **Game Center aplicación** ha sido en desuso y quitados de macOS. Si la aplicación usa GameKit, lo _debe_ presentar su propia interfaz para mostrar las características de GameKit como marcadores, etcetera. 
- Se ha implementado un nuevo tipo de cuenta de iCloud solo por el [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) clase.
- El nuevo [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) clase proporciona una solución generalizada para administrar el almacenamiento de datos persistentes en Game Center. `GKGameSession` mantiene una lista de los jugadores y la aplicación es responsable de formulario implementar cómo y cuándo se almacena la fecha participante, puede recuperar o intercambiados entre agentes. En muchos casos puede reemplazar a las sesiones de juego coincidencias existentes basada en turnos, coincidencias en tiempo real o juego persistente guardar métodos.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Mejoras de GamePlayKit Framework

La siguiente mejora se realizaron en el marco de trabajo GamePlayKit para macOS Sierra:

- Generación de procedimientos de ruido se ha agregado y se puede usar para mejorar el realismo en texturas aspecto natural, aportar realismo a los movimientos de cámara y ayudar a generar los mundos de juegos enriquecidos.
- Utilice particiones espaciales para particionar los datos del mundo de juegos para la búsqueda eficaz.
- Estratega de Monte Carlo nuevo ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) se ha agregado para el cálculo de movimiento exhaustiva de posibles.
- Se ha agregado una nueva API de árbol de decisión ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) y [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) para mejorar la inteligencia artificial de creación de juego.
- Se ha agregado compatibilidad con 3D al agente existente y los comportamientos de búsqueda de ruta con el nuevo [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) y [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) clases.
- Use la nueva [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) clase para proporcionar rutas de acceso de alto rendimiento y aspecto natural.
- El nuevo [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) y [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) make clases combinación GameplayKit y SpriteKit más fácil que nunca.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Mejoras de metal Framework

La siguiente mejora se realizaron en el marco de trabajo del sistema operativo para macOS Sierra:

- Ahora pueden usar los juegos y aplicaciones 3D _teselación_ eficazmente representar geometría a través de la GPU y escenas complejas.
- Utilice la especialización de la función para crear una recopilación altamente optimizadas de material y funciones de la combinación de luz de una escena.
- Proporcionar un mayor control de asignación de recursos para optimizar el rendimiento del sistema operativo basadas en aplicaciones con montones de recursos y destinos de representación Memoryless.

Para obtener más información, consulte Apple [Guía de programación de Metal](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Mejoras en el marco de trabajo de E/S modelo

La siguiente mejora se realizaron en el marco de trabajo de E/S de modelo para macOS Sierra:

- Ahora se admite el formato de archivo USD.
- Use la nueva `MDLMaterialPropertyGraph` clase para admitir fácilmente en tiempo de ejecución cambia a los modelos.
- Firmado se ha agregado compatibilidad para el campo de distancia del [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) clase.
- Use la nueva `MDLLightProbeIrradianceDataSource` clase para ayudar en la selección de ubicación de sondeo de luz.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Mejoras del marco de fotos

La siguiente mejora se realizaron en el marco de fotos para macOS Sierra:

- Edición de fotos en vivo ahora está disponible para las aplicaciones que admiten el marco de fotos y a las extensiones de edición de fotos (para su uso dentro de las fotos y cámara aplicaciones).
- Use la nueva [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) clase aplicar ediciones a tanto el vídeo todavía contenido de fotos en vivo.
- Use la [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) y [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) clases para aprovechar las ventajas de la nueva característica de procesador de imagen básica para realizar modificaciones.
- Para admitir las fotos en vivo, el [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) y [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) se han trasladado las clases de iOS para macOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Mejoras de SceneKit Framework

La siguiente mejora se realizaron en el marco de trabajo de SceneKit para macOS Sierra:

- Ahora incluye un nuevo sistema físicamente en función de representación (PBR) para obtener resultados más realistas con la creación de activos más sencillo.
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Mejoras de seguridad de Framework

La siguiente mejora se realizaron en el marco de seguridad para macOS Sierra:

- El `SecKey` interfaz se ha modernizado y unificada en todas las plataformas (iOS, tvOS, watchOS y macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Mejoras de SpriteKit Framework

La siguiente mejora se realizaron en el marco de trabajo de SpriteKit para macOS Sierra:

- Tilemaps ahora son compatibles con las formas de mosaico cuadrado, hexagonal e isométrica para 2D, 2.5D y juegos de desplazamiento lateral con el `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` y `SKTileSet` clases.
- Use la nueva `SKWarpGeometry` clase estiramiento o distorsionar [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) o [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) representación. El nuevo [SKAction](https://developer.apple.com/reference/spritekit/skaction) clase puede usarse para animar las transiciones entre los efectos de warp.
- Sombreadores personalizados pueden proporcionar atributos (`SKAttribute`) que puede configurarse por separado por cada nodo que utiliza el sombreador proporcionando un valor de atributo (`SKAttributeValue`).
- El [SKView](https://developer.apple.com/reference/spritekit/skview) clase proporciona varios métodos nuevos para darle un mayor control sobre cuándo y cómo se representa una escena.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Nuevos marcos de trabajo

Se han agregado las siguientes plataformas para macOS Sierra:

- **Marco de intenciones** -este marco de trabajo permite que la aplicación examinar las interacciones (por ejemplo, las acciones de usuario o la ubicación) y actuar en función de esa información.
- **SafariServices Framework** -este marco de trabajo permite que la aplicación desarrollar extensiones de aplicación de Safari (por ejemplo, bloqueadores de elementos de contenido) para iOS y macOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://developer.xamarin.com/samples/mac/)
- [Novedades en OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
