---
title: Interfaz de usuario
description: "Este artículo contiene vínculos a las guías que describen los distintos controles de interfaz de usuario de Mac OS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: a8cb9488849dafc2cd720ecf59d654009a9ad781
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="user-interface"></a>Interfaz de usuario

_Este artículo contiene vínculos a las guías que describen los distintos controles de interfaz de usuario de Mac OS._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tendrá acceso a la misma interfaz de usuario que controla un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener las Interfaces de usuario (o si lo desea crearlos directamente en código de C#). 

Las guías siguientes proporcionan información detallada sobre cómo trabajar con elementos de interfaz de usuario de Mac OS en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en cada artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

Este artículo explica cómo trabajar con ventanas y paneles en una aplicación Xamarin.Mac. Describe cómo crear y mantener ventanas y paneles en el generador de interfaz y Xcode, cargar ventanas y paneles de `.storyboard` o `.xib` archivos, con Windows y responde a Windows en el código de C#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Cuadros de diálogo](~/mac/user-interface/dialog.md)

Este artículo explica cómo trabajar con cuadros de diálogo y ventanas modales en una aplicación Xamarin.Mac. En él, se describe cómo crear y mantener ventanas modales en Xcode e Interface Builder, trabajar con cuadros de diálogo estándar, mostrar y responder a ventanas en código de C#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertas](~/mac/user-interface/alert.md)

Este artículo explica cómo trabajar con alertas en una aplicación Xamarin.Mac. En él, se describe cómo crear y visualizar alertas desde el código de C# y cómo responder a las alertas.

## <a name="menusmacuser-interfacemenumd"></a>[Menús](~/mac/user-interface/menu.md)

Los menús se usan en distintas partes de la interfaz de usuario de la aplicación de Mac; desde el menú principal de la aplicación en la parte superior de la pantalla para los menús emergentes y contextuales que pueden aparecer en cualquier parte en una ventana. Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac. En este artículo, se explica cómo trabajar con menús de Cocoa en una aplicación de Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controles estándar](~/mac/user-interface/standard-controls.md)

Trabajar con los controles de AppKit estándar, como botones, etiquetas, campos de texto, casillas de verificación y controles segmentada en una aplicación Xamarin.Mac. Esta guía tratará agregarlas a un diseño de la interfaz de usuario en el generador de interfaz de Xcode, exponerlas al código mediante las salidas y las acciones y trabajar con controles de AppKit en código C#.

 
## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barras de herramientas](~/mac/user-interface/toolbar.md)

Este artículo explica cómo trabajar con las barras de herramientas en una aplicación Xamarin.Mac. En él, se describe cómo crear y mantener las barras de herramientas en Interface Builder y Xcode, cómo exponer los elementos de la barra de herramientas al código mediante salidas y acciones, habilitar y deshabilitar los elementos de la barra de herramientas y finalmente responder a los elementos de la barra de herramientas en código de C#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Vistas de tabla](~/mac/user-interface/table-view.md)

Este artículo explica cómo trabajar con vistas de tabla en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de tabla en el generador de interfaz y Xcode, cómo exponer los elementos de vista de tabla al código mediante las salidas y las acciones, rellenar vistas de la tabla y finalmente responde a los elementos de vista de tabla en el código C#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Vistas de esquema](~/mac/user-interface/outline-view.md)

Este artículo explica cómo trabajar con vistas de esquema en una aplicación Xamarin.Mac. En él, se describe cómo crear y mantener vistas de esquema en Interface Builder y Xcode, cómo exponer los elementos de la vista de esquema al código mediante salidas y acciones, rellenar elementos de esquema y finalmente responder a los elementos de la vista de esquema en código de C#.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Listas de origen](~/mac/user-interface/source-list.md)

Este artículo explica cómo trabajar con listas de origen en una aplicación Xamarin.Mac. En él, se describe cómo crear y mantener listas de origen en Interface Builder y Xcode, cómo exponer los elementos de las listas de origen al código mediante salidas y acciones, rellenar elementos de listas de origen y finalmente responder a los elementos de la lista de origen en código de C#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Vistas de colección](~/mac/user-interface/collection-view.md)

Este artículo explica cómo trabajar con vistas de colección en una aplicación Xamarin.Mac. En él, se describe cómo crear y mantener vistas de colección en Interface Builder y Xcode, cómo exponer los elementos de la vista de colección al código mediante salidas y acciones, rellenar vistas de colección y finalmente responder a las vistas de colección en código de C#.

## <a name="creating-custom-user-controlsmacuser-interfacecustom-controlsmd"></a>[Crear controles de usuario personalizados](~/mac/user-interface/custom-controls.md)

Este artículo trata la creación de controles de interfaz de usuario personalizados (mediante la adquisición de `NSControl`), una interfaz personalizada para el control de dibujo y crear acciones personalizadas que pueden usarse con el generador de interfaz de Xcode.

## <a name="mac-samples-gallery"></a>Galería de ejemplos de Mac

También se recomienda echar un vistazo a la [Galería de ejemplos de Mac](http://developer.xamarin.com/samples/mac/all/), incluye una gran cantidad de código listos para usar que puede ayudarle a obtener rápidamente un proyecto Xamarin.Mac del suelo.

## <a name="related-links"></a>Vínculos relacionados

- [macOS directrices de interfaz humana](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
