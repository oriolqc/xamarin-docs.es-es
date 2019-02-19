---
title: Compatibilidad con idiomas en Xamarin de programación
description: 'Este documento describe varios lenguajes de programación compatibles con Xamarin. Se trata de C#, F #, Visual Basic.NET portátil y plantillas de Razor.'
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 715f63a0be54ba3342bd63c1c76d89656313359a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781681"
---
# <a name="programming-language-support-in-xamarin"></a>Compatibilidad con idiomas en Xamarin de programación

## <a name="c"></a>C# 

###  <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)

Versión 5 de C# introdujo dos nuevas palabras clave para expresar las operaciones asincrónicas: async y await. Estas palabras clave le permiten escribir código simple que utiliza la biblioteca TPL para ejecutar las operaciones de larga duración (por ejemplo, el acceso a la red) en otro subproceso y obtener acceso fácilmente a los resultados de la finalización. Las versiones más recientes de Xamarin.iOS y Xamarin.Android admiten async y await: este documento proporciona explicaciones y un ejemplo de cómo usar la nueva sintaxis con Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Características del lenguaje C# 6](~/cross-platform/platform/csharp-six.md)

La versión más reciente del lenguaje de C#, versión 6, continua evolucionando el idioma para reducir la repetición de código, mejorar claridad y coherencia. Sintaxis de inicialización de limpieza, la posibilidad de utilizar await en bloques de catch/finally y el operador condicional null ? son especialmente útiles.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Creación de aplicaciones móviles con F # y Xamarin.

##  <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Visual Basic.NET portátil](~/cross-platform/platform/visual-basic/index.md)

Visual Studio admite la creación de bibliotecas de clases Portable con Visual Basic.NET que, a continuación, se pueden incorporar en aplicaciones de Xamarin. Este artículo muestra cómo crear una nueva PCL de Visual Basic y, a continuación, utilizarlo en una aplicación de ejemplo Xamarin.iOS y Xamarin.Android, Windows Phone.

##  <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Vistas de creación HTML mediante plantillas de Razor](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin permite a los desarrolladores aprovechar el motor de plantillas de Razor, apareció inicialmente con ASP.NET MVC, junto con C# para combinar fácilmente los datos con HTML, Javascript y CSS sin las complicaciones de la creación manual de las cadenas HTML en el código.
Este artículo demuestra cómo usar plantillas de Razor con Xamarin para iOS y Android.
