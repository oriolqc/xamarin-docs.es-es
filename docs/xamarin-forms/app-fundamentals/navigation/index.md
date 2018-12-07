---
title: Navegación por Xamarin.Forms
description: En esta guía se explica cómo navegar por las aplicaciones de Xamarin.Forms. Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo de página que se use.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994733"
---
# <a name="xamarinforms-navigation"></a>Navegación por Xamarin.Forms

_Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo de página que se use._

![](images/page-types.png "Tipos de páginas de Xamarin.Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navegación jerárquica](hierarchical.md)

La clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) proporciona una experiencia de navegación jerárquica en la que el usuario puede navegar por las páginas hacia delante y hacia atrás, si quiere. La clase implementa la navegación como una pila de objetos [`Page`](xref:Xamarin.Forms.Page) en la que el último en entrar es el primero en salir (LIFO).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) de Xamarin.Forms consiste en una lista de pestañas y un área más grande de detalles. Cada pestaña carga contenido en el área de detalles.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage) de Xamarin.Forms es una página que los usuarios pueden deslizar de lado a lado para navegar por páginas de contenido, como una galería.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) de Xamarin.Forms es una página que administra dos páginas de información relacionada: una página maestra que presenta los elementos y una página de detalles que muestra los detalles sobre los elementos de la página maestra.

## <a name="modal-pagesmodalmd"></a>[Páginas modales](modal.md)

Xamarin.Forms también es compatible con las páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea.

## <a name="displaying-pop-upspop-upsmd"></a>[Visualización de elementos emergentes](pop-ups.md)

Xamarin.Forms proporciona dos elementos de la interfaz de usuario similares a elementos emergentes: una alerta y una hoja de acción. Estos elementos de interfaz se pueden usar para pedir a los usuarios preguntas sencillas y guiar a los usuarios a través de tareas.
