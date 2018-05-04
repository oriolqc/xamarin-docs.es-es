---
title: Compatibilidad con lenguajes
description: Conceptos y características de la aplicación multiplataforma.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: f22bb1b37f3e03f63c9e47cf71d5df57ade0042f
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="language-support"></a>Compatibilidad con lenguajes

Esta sección contiene los documentos que explican algunos conceptos y características más avanzadas de aplicación multiplataforma.

## <a name="c"></a>C# 
###  <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)

Versión 5 de C# introdujo dos nuevas palabras clave para expresar las operaciones asincrónicas: async y await. Estas palabras clave le permiten escribir código simple que utiliza la biblioteca TPL para ejecutar las operaciones de larga duración (por ejemplo, el acceso a la red) en otro subproceso y obtener acceso fácilmente a los resultados de la finalización. Las versiones más recientes de Xamarin.iOS y Xamarin.Android admiten async y await: este documento proporciona explicaciones y un ejemplo de cómo usar la nueva sintaxis con Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Características del lenguaje C# 6](~/cross-platform/platform/csharp-six.md)

La versión más reciente del lenguaje de C#, versión 6, continúa evolucionando el idioma para que tenga menos reutilizable, mejorar la claridad y coherencia más. Sintaxis de inicialización de limpieza y la posibilidad de usar `await` en `catch/finally` bloques y el condicional null `?` operador son especialmente útiles.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Creación de aplicaciones móviles con F # y Xamarin.

##  <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Visual Basic.NET portátil](~/cross-platform/platform/visual-basic/index.md)

Visual Studio admite la creación de bibliotecas de clases Portable con Visual Basic.NET que, a continuación, se pueden incorporar en aplicaciones de Xamarin. Este artículo muestra cómo crear una nueva PCL de Visual Basic y, a continuación, utilizarlo en una aplicación de ejemplo Xamarin.iOS y Xamarin.Android, Windows Phone.

##  <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Vistas de creación HTML mediante plantillas de Razor](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin permite a los desarrolladores aprovechar el motor de plantillas de Razor, apareció inicialmente con ASP.NET MVC, junto con C# para combinar fácilmente los datos con HTML, Javascript y CSS sin las complicaciones de la creación manual de las cadenas HTML en el código.
Este artículo demuestra cómo usar plantillas de Razor con Xamarin para iOS y Android.
