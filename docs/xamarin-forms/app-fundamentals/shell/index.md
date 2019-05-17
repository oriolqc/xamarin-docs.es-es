---
title: Xamarin.Forms Shell
description: En esta guía se explica cómo usar Xamarin.Forms Shell, que reduce la complejidad de las aplicaciones de Xamarin.Forms al proporcionar las características fundamentales que requieren la mayoría de las aplicaciones.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 7699b39a6df6c64ae9a481d9171f23dc6a8eba57
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054195"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

Xamarin.Forms Shell reduce la complejidad del desarrollo de aplicaciones móviles al proporcionar las características fundamentales que requieren la mayoría de aplicaciones móviles. Esto incluye una experiencia de usuario de navegación común, un esquema de navegación basado en URI y un controlador de búsqueda integrada.

## <a name="flyoutflyoutmd"></a>[Control flotante](flyout.md)

El control flotante es el menú raíz de una aplicación de Shell y es accesible por medio de un icono o al deslizar el dedo desde el lado de la pantalla. El control flotante consta de un encabezado opcional, elementos de control flotante y elementos de menú opcionales.

## <a name="tabstabsmd"></a>[Pestañas](tabs.md)

Después de un control flotante, el siguiente nivel de navegación en una aplicación de Shell es la barra de pestañas de la parte inferior. Cuando una pestaña contiene más de una página, las páginas son navegables mediante las pestañas principales.

## <a name="navigationnavigationmd"></a>[Navegación](navigation.md)

Las aplicaciones de Shell pueden usar un esquema de navegación basado en URI que emplea rutas para navegar a cualquier página de la aplicación, sin tener que seguir una jerarquía de navegación establecida.

## <a name="searchsearchmd"></a>[Búsqueda](search.md)

Las aplicaciones de Shell pueden usar la funcionalidad de búsqueda integrada que se proporciona en un cuadro de búsqueda que se puede agregar a la parte superior de cada página.

## <a name="custom-rendererscustomrenderersmd"></a>[Representadores personalizados](customrenderers.md)

Las aplicaciones de Shell son muy personalizables mediante las propiedades y los métodos que exponen las distintas clases de Shell. Sin embargo, también es posible crear a un representador personalizado de Shell cuando se requieren personalizaciones más sofisticadas específicas de la plataforma.
