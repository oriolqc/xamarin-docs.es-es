---
title: "Navegación"
description: "Xamarin.Forms ofrece una serie de experiencias de navegación de página diferente, dependiendo del tipo de página que se va a usar."
ms.topic: article
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 180eed39a1aa7bf352665b9a0198a88c2fcbc5b5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="navigation"></a>Navegación

_Xamarin.Forms ofrece una serie de experiencias de navegación de página diferente, dependiendo del tipo de página que se va a usar._

![](images/page-types.png "Tipos de páginas de Xamarin.Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navegación jerárquica](hierarchical.md)

La clase [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) proporciona una experiencia de navegación jerárquica en la que el usuario puede navegar por las páginas hacia delante y hacia atrás, si quiere. La clase implementa la navegación como una pila de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) en la que el último en entrar es el primero en salir (LIFO).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

El Xamarin.Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) consta de una lista de pestañas y un área más grande de detalle, con cada pestaña cargar contenido en el área de detalles.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

El Xamarin.Forms [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) es una página que los usuarios pueden Deslizar rápidamente de lado a lado para navegar por las páginas de contenido, como una galería.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

El Xamarin.Forms [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) es una página que administra dos páginas de información relacionada: una página maestra que presenta los elementos y una página de detalles que muestra los detalles acerca de los elementos en la página maestra.

## <a name="modal-pagesmodalmd"></a>[Páginas modales](modal.md)

Xamarin.Forms también proporciona compatibilidad para las páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea.

## <a name="displaying-pop-upspop-upsmd"></a>[Mostrar elementos emergentes](pop-ups.md)

Xamarin.Forms ofrece dos elementos de la interfaz de usuario emergente up como: una alerta así como una hoja de acción. Estos elementos de la interfaz se pueden utilizar para pedir a los usuarios preguntas sencillas y guiar a los usuarios a través de tareas.

