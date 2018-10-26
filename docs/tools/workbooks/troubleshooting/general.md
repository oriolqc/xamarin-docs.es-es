---
title: Problemas conocidos y soluciones alternativas
description: Este documento describe problemas conocidos y soluciones para Xamarin Workbooks. Describe problemas CultureInfo, problemas JSON y mucho más.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 221ed97db17da62f513448b6c85d4df205a7cbaf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110321"
---
# <a name="known-issues--workarounds"></a>Problemas conocidos y soluciones alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistencia de CultureInfo entre celdas

Establecer `System.Threading.CurrentThread.CurrentCulture` o `System.Globalization.CultureInfo.CurrentCulture` no se conservan entre las celdas del libro en destinos de los libros de Mono (Mac, iOS y Android) debido a un [errores en Mono `AppContext.SetSwitch` ] [ appcontext-bug] implementación .

### <a name="workarounds"></a>Soluciones

* Establecer el local de dominio de aplicación `DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* O actualización de libros 1.2.1 o posterior, que se vuelva a escribir las asignaciones a `System.Threading.CurrentThread.CurrentCulture` y `System.Globalization.CultureInfo.CurrentCulture` para proporcionar el comportamiento deseado (evitar el error Mono).

## <a name="unable-to-use-newtonsoftjson"></a>No se puede usar Newtonsoft.Json

### <a name="workaround"></a>Solución

* Actualizar a libros 1.2.1, que instalarán Newtonsoft.Json 9.0.1.
  Libros 1.3, actualmente en el canal alfa, admite las versiones 10 y versiones más recientes.

### <a name="details"></a>Detalles

Se lanzó Newtonsoft.Json 10 que se incrementa su dependencia de Microsoft.CSharp que entra en conflicto con los libros de versión se incluye para admitir `dynamic`. Esto se trata en la versión preliminar de los libros 1.3, pero por ahora hemos trabajado solucionar este problema mediante Newtonsoft.Json anclaje específicamente a la versión 9.0.1.

Paquetes de NuGet explícitamente según Newtonsoft.Json 10 o posterior solo se admiten en la versión 1.3 de libros, actualmente en el canal alfa.

## <a name="code-tooltips-are-blank"></a>Información sobre herramientas de código es en blanco

Hay un [errores en el editor Monaco] [ monaco-bug] en Safari/WebKit, que se utiliza en la aplicación Workbooks de Mac, que es el resultado en el procesamiento de información sobre herramientas de código sin texto.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solución

* Al hacer clic en la información sobre herramientas después de que aparezca forzará el texto que se presenta.

* O actualizar a libros 1.2.1 o posterior

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Faltan los representadores de SkiaSharp en la versión 1.3 de libros

A partir de los libros 1.3, hemos quitado los representadores de SkiaSharp que lanzamos al mercado en los libros 0.99.0, en favor de SkiaSharp ofrece los representadores en Sí, mediante nuestros [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Solución

* Actualizar SkiaSharp a la versión más reciente de NuGet. En el momento de escribir este artículo, se trata de 1.57.1.

## <a name="related-links"></a>Vínculos relacionados

- [Informar de errores](~/tools/workbooks/install.md#reporting-bugs)
