---
title: Navegación por Xamarin.Forms
description: En esta guía se explica cómo navegar por las aplicaciones de Xamarin.Forms. Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo de página que se use.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: f67ab15466da118d12c280d597972d2d11f8e600
ms.sourcegitcommit: 6ad272c2c7b0c3c30e375ad17ce6296ac1ce72b2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66178116"
---
# <a name="xamarinforms-navigation"></a>Navegación por Xamarin.Forms

_Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo de página que se use._

![](images/page-types.png "Tipos de páginas de Xamarin.Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navegación jerárquica](hierarchical.md)

La clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) proporciona una experiencia de navegación jerárquica en la que el usuario puede navegar por las páginas hacia delante y hacia atrás, si quiere. La clase implementa la navegación como una pila de objetos [`Page`](xref:Xamarin.Forms.Page) en la que el último en entrar es el primero en salir (LIFO).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) de Xamarin.Forms consta de una lista de pestañas y un área de detalles mayor. Cada pestaña carga contenido en el área de detalles.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage) de Xamarin.Forms es una página que los usuarios pueden deslizar de lado a lado para navegar por páginas de contenido, como una galería.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) de Xamarin.Forms es una página que administra dos páginas de información relacionada: una página maestra que presenta los elementos y una página de detalles que muestra los detalles sobre los elementos de la página maestra.

## <a name="modal-pagesmodalmd"></a>[Páginas modales](modal.md)

Xamarin.Forms también es compatible con las páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea.
