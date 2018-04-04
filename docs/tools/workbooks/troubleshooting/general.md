---
title: Problemas conocidos y soluciones alternativas
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 0dcff6402612fb8dad1e80f9158298b1171da75a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="known-issues--workarounds"></a>Problemas conocidos y soluciones alternativas

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistencia de CultureInfo en las celdas

Establecer `System.Threading.CurrentThread.CurrentCulture` o `System.Globalization.CultureInfo.CurrentCulture` no se conserva en las celdas del libro en destinos de libros basados en Mono (Mac, iOS y Android) debido a un [error en de Mono `AppContext.SetSwitch` ] [ appcontext-bug] implementación .

### <a name="workarounds"></a>Soluciones

* Establecer el local de dominio de aplicación `DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* O bien, actualización de libros 1.2.1 o versiones más recientes, que se vuelva a escribir las asignaciones a `System.Threading.CurrentThread.CurrentCulture` y `System.Globalization.CultureInfo.CurrentCulture` para proporcionar el comportamiento deseado (solucionar el error Mono).

## <a name="unable-to-use-newtonsoftjson"></a>No se puede usar Newtonsoft.Json

### <a name="workaround"></a>Solución

* Actualizar a libros 1.2.1, que instalarán Newtonsoft.Json 9.0.1.
  Libros 1.3, actualmente en el canal alfa, es compatible con las versiones 10 y versiones más recientes.

### <a name="details"></a>Detalles

Newtonsoft.Json 10 se liberó que aumenta su dependencia de Microsoft.CSharp que entra en conflicto con los libros de una versión que se incluye para admitir empieza a escasear `dynamic`. Esto se soluciona en la versión de vista previa de los libros 1.3, pero por ahora hemos trabajado resolver este problema mediante anclado Newtonsoft.Json específicamente a la versión 9.0.1.

Paquetes de NuGet explícitamente según Newtonsoft.Json 10 o versiones más recientes solo se admiten en los libros 1.3, actualmente en el canal alfa.

## <a name="code-tooltips-are-blank"></a>Información sobre herramientas de código es en blanco

Hay un [error en el editor Monaco] [ monaco-bug] en Safari/WebKit, que se utiliza en la aplicación de los libros de Mac, que da como resultado la representación de información sobre herramientas de código sin texto.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solución

* Al hacer clic en la información sobre herramientas después de que aparezca, se forzará el texto que se va a representar.

* O actualizar a libros 1.2.1 o posterior

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Faltan los representadores de SkiaSharp en libros 1.3

A partir de los libros 1.3, hemos quitado los representadores de SkiaSharp que se incluye en los libros 0.99.0, en favor de SkiaSharp proporcionando los representadores de sí mismo, con nuestra [SDK] [/ guías/entre-plataforma/libros/sdk /].

### <a name="workaround"></a>Solución

* Actualizar SkiaSharp a la versión más reciente de NuGet. En el momento de escribir este artículo, se trata de 1.57.1.

## <a name="related-links"></a>Vínculos relacionados

- [Informes errores](~/tools/workbooks/install.md#reporting-bugs)
