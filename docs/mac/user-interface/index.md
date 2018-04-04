---
title: interfaz de usuario de Mac OS
description: Este artículo contiene vínculos a las guías que describen los distintos controles de interfaz de usuario de Mac OS.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: d40faa29f2fe278377bf4eae42a032f3dc9086ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="macos-user-interface"></a>interfaz de usuario de Mac OS

_Este artículo contiene vínculos a las guías que describen los distintos controles de interfaz de usuario de Mac OS._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, se tiene acceso al mismo usuario controles de interfaz que un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener las interfaces de usuario (o si lo desea crearlos directamente en código de C#).

Las guías siguientes proporcionan información detallada sobre cómo trabajar con elementos de interfaz de usuario de Mac OS en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en cada artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) de documento, tal y como se explica la `Register` y `Export` atributos que utiliza el cable de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

Este artículo explica cómo trabajar con ventanas y paneles en una aplicación Xamarin.Mac. Describe cómo crear y mantener ventanas y paneles en Xcode y el generador de interfaz, ventanas de carga y paneles de archivos .storyboard o .xib, con windows y responde a las ventanas de código de C#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Cuadros de diálogo](~/mac/user-interface/dialog.md)

Este artículo explica cómo trabajar con cuadros de diálogo y ventanas modales en una aplicación Xamarin.Mac. Describe cómo crear y mantener ventanas modales en Xcode y el generador de interfaz, trabajar con cuadros de diálogo estándar y mostrar y responde a las ventanas de código de C#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertas](~/mac/user-interface/alert.md)

Este artículo explica cómo trabajar con alertas en una aplicación Xamarin.Mac. Describe cómo crear y mostrar las alertas desde el código C# y responder a las alertas.

## <a name="menusmacuser-interfacemenumd"></a>[Menús](~/mac/user-interface/menu.md)

Los menús se usan en distintas partes de la interfaz de usuario de la aplicación de Mac; desde el menú principal de la aplicación en la parte superior de la pantalla para los menús emergentes y menús contextuales que pueden aparecer en cualquier parte en una ventana. Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac. Este artículo explica cómo trabajar con menús cacao en una aplicación Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controles estándar](~/mac/user-interface/standard-controls.md)

Trabajar con los controles de AppKit estándar, como botones, etiquetas, campos de texto, casillas de verificación y controles segmentados en una aplicación de Xamarin.Mac. Esta guía tratará agregarlas a un diseño de la interfaz de usuario en el generador de interfaz de Xcode, exponerlas al código mediante las salidas y las acciones y trabajar con controles de AppKit en código C#.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barras de herramientas](~/mac/user-interface/toolbar.md)

Este artículo explica cómo trabajar con las barras de herramientas en una aplicación Xamarin.Mac. Describe cómo crear y mantener las barras de herramientas en Xcode y el generador de interfaz, cómo exponer los elementos de la barra de herramientas al código mediante las salidas y las acciones, habilitar y deshabilitar elementos de la barra de herramientas y finalmente responde a los elementos de la barra de herramientas en código C#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Vistas de tabla](~/mac/user-interface/table-view.md)

Este artículo explica cómo trabajar con vistas de tabla en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de tabla en Xcode y el generador de interfaz, cómo exponer la vista de tabla los elementos en el código mediante las salidas y las acciones, rellenar vistas de tabla y responde a los elementos de vista de tabla en el código C#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Vistas de esquema](~/mac/user-interface/outline-view.md)

Este artículo explica cómo trabajar con vistas de esquema en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de esquema en Xcode y el generador de interfaz, cómo para exponer la vista de esquema con los elementos en el código mediante las salidas y las acciones, rellenar vistas de esquema y responde para describir elementos de vista de código de C#.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Listas de origen](~/mac/user-interface/source-list.md)

Este artículo explica cómo trabajar con listas de origen en una aplicación Xamarin.Mac. Describe cómo crear y mantener listas de origen en Xcode y el generador de interfaz, cómo exponer elementos de la lista de origen al código mediante las salidas y las acciones, rellenar listas de origen y responder a los elementos de la lista de código fuente en código C#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Vistas de colección](~/mac/user-interface/collection-view.md)

Este artículo explica cómo trabajar con vistas de colección en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de colección en Xcode y el generador de interfaz, cómo exponer la vista de colección de elementos en el código mediante las salidas y las acciones, rellenar vistas de colección y responde a las vistas de colección en código C#.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Creación de controles personalizados](~/mac/user-interface/custom-controls.md)

Este artículo trata de crear controles de interfaz de usuario personalizada (heredando de `NSControl`), una interfaz personalizada para el control de dibujo y la creación de acciones personalizadas que pueden usarse con el generador de interfaz de Xcode.

## <a name="mac-samples-gallery"></a>Galería de ejemplos de Mac

También se recomienda echar un vistazo a la [Galería de ejemplos de Mac](https://developer.xamarin.com/samples/mac/all/). Incluye una gran cantidad de código listos para usar que puede ayudarle a obtener rápidamente un proyecto Xamarin.Mac del suelo.

## <a name="related-links"></a>Vínculos relacionados

- [macOS directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
