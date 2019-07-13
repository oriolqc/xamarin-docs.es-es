---
title: Programación de la compatibilidad con idiomas en Xamarin
description: Este documento describe los diversos lenguajes de programación compatibles con Xamarin. Describe C#, F#, portable Visual Basic.NET y plantillas de Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 6c0b8e6de0c414fb708c4027f4c536a21b6b011a
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864375"
---
# <a name="programming-language-support-in-xamarin"></a>Programación de la compatibilidad con idiomas en Xamarin

## <a name="c"></a>C# 

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)

Versión 5 de C# introdujo dos nuevas palabras clave para expresar las operaciones asincrónicas: async y await. Estas palabras clave le permiten escribir código simple que usa la biblioteca TPL para ejecutar operaciones de larga ejecución (por ejemplo, el acceso a la red) en otro subproceso y acceder fácilmente a los resultados al finalizar. Las últimas versiones de Xamarin.iOS y Xamarin.Android, compatibilidad con async y await: este documento proporciona explicaciones y un ejemplo del uso de la nueva sintaxis con Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Características del lenguaje C# 6](~/cross-platform/platform/csharp-six.md)

La versión más reciente de la C# , versión 6, idioma sigue evolucionando el lenguaje para tener menos repetitivo, ha mejorado la claridad y coherencia más. Sintaxis de inicialización más limpia, la capacidad de usar `await` en `catch/finally` bloques y el condicional de null `?` operador son especialmente útiles.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Crear aplicaciones móviles con F# y Xamarin.

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Portable Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio admite la creación de bibliotecas de clases portables utilizando Visual Basic.NET que, a continuación, se puede incorporar en las aplicaciones de Xamarin. En este artículo se muestra cómo crear una nueva PCL de Visual Basic y, a continuación, utilizarlo en una aplicación de ejemplo de Xamarin.iOS, Xamarin.Android y Windows Phone.

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Vistas HTML de creación mediante plantillas de Razor](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin permite a los desarrolladores aprovechar el motor de plantillas de Razor, presentado originalmente con ASP.NET MVC, junto con C# se combinen fácilmente datos con HTML, Javascript y CSS sin la molestia de crear manualmente las cadenas HTML en el código.
En este artículo se muestra cómo usar las plantillas de Razor con Xamarin para iOS y Android.
