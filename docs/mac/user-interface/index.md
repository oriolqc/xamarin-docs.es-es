---
title: controles de interfaz de usuario de macOS en Xamarin.Mac
description: Este documento incluye vínculos a guías que describen los distintos controles de interfaz de usuario disponibles para los desarrolladores de Xamarin.Mac. Contenido vinculado echa un vistazo a windows, los cuadros de diálogo, alertas, menús, barras de herramientas, las vistas de tablas, vistas de esquema y más.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/27/2018
ms.openlocfilehash: a12553cf0b7b9584bb8ff7bc04ed326ad4a7ad2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61281602"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>controles de interfaz de usuario de macOS en Xamarin.Mac

_Este artículo contiene vínculos a guías que describen los distintos controles de interfaz de usuario de macOS._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, se tiene acceso al mismo usuario controles de interfaz que un desarrollador que trabaja *Objective-C* y *Xcode* does. Ya que Xamarin.Mac se integra directamente con Xcode, puede usar Xcode _Interface Builder_ para crear y mantener las interfaces de usuario (o bien, opcionalmente, crearlos directamente en código de C#).

Las guías siguientes ofrecen información detallada sobre cómo trabajar con elementos de interfaz de usuario de macOS en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en todos los artículos.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) de documentos, tal como explica el `Register` y `Export` atributos usados para conexión de seguridad de las clases de C# para objetos de Objective-C y elementos de interfaz de usuario.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

En este artículo se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin.Mac. Describe cómo crear y mantener ventanas y paneles en Xcode e Interface Builder, carga de ventanas y paneles desde archivos .storyboard o .xib, usa windows y responder a ventanas en código C#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Cuadros de diálogo](~/mac/user-interface/dialog.md)

En este artículo se explica cómo trabajar con cuadros de diálogo y ventanas modales en una aplicación de Xamarin.Mac. Describe cómo crear y mantener ventanas modales en Xcode e Interface Builder, trabajar con cuadros de diálogo estándar y mostrar y responder a ventanas en código C#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertas](~/mac/user-interface/alert.md)

En este artículo se explica cómo trabajar con alertas en una aplicación de Xamarin.Mac. Se describe cómo crear y visualizar alertas desde el código C# y responder a las alertas.

## <a name="menusmacuser-interfacemenumd"></a>[Menús](~/mac/user-interface/menu.md)

Los menús se usan en distintas partes de la interfaz de usuario de la aplicación de Mac; desde el menú principal de la aplicación en la parte superior de la pantalla hasta menús emergentes y menús contextuales que pueden aparecer en cualquier lugar en una ventana. Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac. En este artículo se explica cómo trabajar con menús de Cocoa en una aplicación de Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controles estándar](~/mac/user-interface/standard-controls.md)

Trabajar con los controles de AppKit estándar, como botones, etiquetas, campos de texto, casillas y controles segmentados en una aplicación de Xamarin.Mac. Esta guía cubre agregarlas a un diseño de la interfaz de usuario en Interface Builder de Xcode, exponerlos al código mediante salidas y acciones y trabajar con controles de AppKit en código C#.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barras de herramientas](~/mac/user-interface/toolbar.md)

En este artículo se explica cómo trabajar con las barras de herramientas en una aplicación de Xamarin.Mac. Describe cómo crear y mantener las barras de herramientas en Xcode e Interface Builder, cómo exponer los elementos de la barra de herramientas al código mediante salidas y acciones, habilitar y deshabilitar los elementos de la barra de herramientas y finalmente responder a los elementos de la barra de herramientas de código de C#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Vistas de tabla](~/mac/user-interface/table-view.md)

En este artículo se explica cómo trabajar con vistas de tabla en una aplicación de Xamarin.Mac. Describe cómo crear y mantener vistas de tabla en Xcode e Interface Builder, cómo exponer la vista de tabla los elementos en el código mediante salidas y acciones, rellenar vistas de tabla y responder a los elementos de la vista de tabla en el código C#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Vistas de esquema](~/mac/user-interface/outline-view.md)

En este artículo se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin.Mac. Describe cómo crear y mantener vistas de esquema en Xcode e Interface Builder, cómo exponer la vista de esquema elementos en el código mediante salidas y acciones, rellenar vistas de esquema y responder para ver los elementos en el código de C# de esquema.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Listas de origen](~/mac/user-interface/source-list.md)

En este artículo se explica cómo trabajar con listas de origen en una aplicación de Xamarin.Mac. Describe cómo crear y mantener listas de origen en Xcode e Interface Builder, exponer elementos de lista de origen al código mediante salidas y acciones, rellenar las listas de origen y responder a los elementos de lista de origen en código C#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Vistas de colección](~/mac/user-interface/collection-view.md)

En este artículo se explica cómo trabajar con las vistas de colección en una aplicación de Xamarin.Mac. Describe cómo crear y mantener las vistas de colección en Xcode e Interface Builder, cómo exponer la vista de colección de elementos en código mediante salidas y acciones, rellenar vistas de colección y responder a las vistas de colección en código C#.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Creación de controles personalizados](~/mac/user-interface/custom-controls.md)

Este artículo trata sobre la creación de controles de interfaz de usuario personalizado (heredando de `NSControl`), una interfaz personalizada para el control de dibujo y la creación de acciones personalizadas que se pueden usar con Interface Builder de Xcode.

## <a name="mac-samples-gallery"></a>Galería de ejemplos de Mac

También se recomienda echar un vistazo a la [Galería de ejemplos de Mac](https://developer.xamarin.com/samples/mac/all/). Incluye una gran cantidad de código listos para usar que puede ayudarle a obtener un proyecto de Xamarin.Mac en marcha rápidamente.

## <a name="related-links"></a>Vínculos relacionados

- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
