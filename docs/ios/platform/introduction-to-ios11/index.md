---
title: Introducción a iOS 11
description: Este documento incluye vínculos a diversas guías que describen las características de iOS 11, incluidos ARKit CoreML, MapKit, PDFKit, SiriKit, el marco de trabajo de visión y mucho más.
ms.prod: xamarin
ms.assetid: 22C38EA6-6DA9-4B92-B41B-814E589033F6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2017
ms.openlocfilehash: 5e75a7872081d82c289db0312bbc7d84bce77b4e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116724"
---
# <a name="introduction-to-ios-11"></a>Introducción a iOS 11

_Inténtelo de nuevo iOS 11 API con Xamarin._

> [!NOTE]
> Antes de comenzar, revise el [Introducción](get-started.md) página para obtener instrucciones sobre cómo instalar Xcode 9.

![Ejemplo de ARKit](images/arkit.png) ![Colocar objetos de cuentas por cobrar](images/arkit2.png) ![Ejemplo de CoreML](images/coreml.png) ![Ejemplo de MapKit](images/mapkit.png) ![Ejemplo de visión de rectángulos](images/vision1.png) ![Ejemplo de visión de caras](images/vision2.png) ![Arrastrar y colocar de ejemplo](images/drag-drop.png) ![Arrastrar y colocar de ejemplo](images/drag-drop2.png) ![Ejemplo de SiriKit](images/sirikit.png)

iOS 11 incluye muchas características nuevas y mejoras en una variedad de marcos:

## <a name="preparing-your-app-for-ios-11updating-your-appindexmd"></a>[Preparación de la aplicación para iOS 11](updating-your-app/index.md)

Apple ha introducido las actualizaciones de la arquitectura, nuevos cambios visuales y un proceso de conectar iTunes actualizada para iOS 11. Use esta guía para asegurarse de que la aplicación Xamarin.iOS está preparada para la nueva versión.

## <a name="arkitarkitindexmd"></a>[ARKit](arkit/index.md)

ARKit aporta realidad aumentada para iOS, lo que permite a los usuarios interactuar con el mundo a través de la cámara del dispositivo.
Con Xamarin, también puede usar [ARKit con UrhoSharp](arkit/urhosharp.md).

## <a name="coremlcoremlmd"></a>[CoreML](coreml.md)

Modelos de aprendizaje automático se pueden integrar en aplicaciones de iOS 11 con CoreML. El marco de trabajo CoreML proporciona una API sencilla para incorporar los modelos existentes en los proyectos de aplicación para permitir que los problemas que analizarse directamente en la aplicación.

## <a name="corenfccorenfcmd"></a>[CoreNFC](corenfc.md)

7 de iPhone y dispositivos más recientes pueden leer etiquetas cerca de campo Communication (NFC), habilitar aplicaciones detectar productos etiquetados, lugares o cosas en el mundo en torno a ellas.

## <a name="drag-and-dropdrag-and-dropmd"></a>[Arrastrar y colocar](drag-and-drop.md)

El marco de arrastrar y colocar ofrece soporte técnico para iOS para mover los datos con el tacto. En iPad, puede arrastrar dentro y entre las diferentes aplicaciones; mientras se encuentra en el iPhone, puede arrastrar solo dentro de la misma aplicación. Hay compatibilidad para muchos tipos de personalización, incluidos los tipos de datos enriquecidos, animaciones y los gestos multitoque de control.

## <a name="mapkitmapkitmd"></a>[MapKit](mapkit.md)

MapKit tiene una serie de mejoras, incluida la compatibilidad con marcador automática agrupar y agregar una brújula a la vista.

## <a name="pdfkit"></a>PDFKit

PDFKit ahora está disponible en iOS 11, ya que ofrece creación de PDF y funciones a las aplicaciones de edición.

## <a name="sirikitsirikitmd"></a>[SiriKit](sirikit.md)

Siri ahora es compatible con las interacciones aún más, incluidas las listas de notas y otras mejoras, como los nombres de aplicación alternativo.

## <a name="visionvisionmd"></a>[Vision](vision.md)

Aporta una variedad de imagen de las características de procesamiento y análisis a iOS, incluida la detección de caras y reconocimiento, los modelos CoreML, nueva API de detección de código de barras, texto y el horizonte de detección y detección de objetos más general y de seguimiento.

## <a name="samples"></a>Muestras

Tenemos un número de C# [ejemplos](https://developer.xamarin.com/samples/ios/iOS11/) para comenzar:

* [Ejemplo de ARKit](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
* [ARKit colocar objetos](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
* [ARKit y UrhoSharp](arkit/urhosharp.md)
* [Ejemplo de CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreML)
* [Ejemplo de reconocimiento de imágenes de CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition)
* [CoreML con el modelo personalizado de Azure](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
* [Ejemplo de lector CoreNFC etiqueta](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
* [Arrastrar y colocar la vista de tabla](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView)
* [Arrastrar y colocar la vista de colección](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
* [Arrastrar y colocar la vista personalizada](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCustomView)
* [DragBoard arrastrar y colocar ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropDragBoard)
* [Ejemplo de MapKit](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample)
* [Ejemplo de SiriKit](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
* [Ejemplo de marco de trabajo actualizado de fotos](https://developer.xamarin.com/samples/monotouch/ios11/SamplePhotoApp/)
* [Vision y CoreML ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision)
* [Ejemplo de detección de los rectángulos de visión](https://developer.xamarin.com/samples/monotouch/ios11/VisionRects)
* [Ejemplo de detección de caras de visión](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces)
* [Ejemplo de Widgets PDKFit](https://developer.xamarin.com/samples/monotouch/ios11/PDFAnnotationWidgetsAdvanced)
* [Ejemplo de marca de agua PDFKit](https://developer.xamarin.com/samples/monotouch/ios11/PDFDocumentWatermark)

## <a name="more-information"></a>Más información

Para obtener más información en iOS 11, visite Apple [Novedades en iOS 11](https://developer.apple.com/ios/) documentación.


## <a name="related-links"></a>Vínculos relacionados

- [Novedades en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Ejemplos de Xamarin iOS 11](https://developer.xamarin.com/samples/ios/iOS11/)
