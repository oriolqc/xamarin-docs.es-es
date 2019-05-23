---
title: Xamarin.Forms CollectionView
description: El elemento CollectionView es una vista flexible y eficaz para presentar las listas de datos con las especificaciones de diseño diferente.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 79af2bb2ecda8d3d8df9ee9ec40276b0c59b6864
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005311"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

El [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) es una vista flexible y eficaz para presentar las listas de datos con las especificaciones de diseño diferente.

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

Un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se rellena con datos estableciendo sus [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad a cualquier colección que implementa `IEnumerable`. Se puede definir la apariencia de cada elemento de la lista estableciendo la [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Diseño](layout.md)

De forma predeterminada, un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar las cuadrículas y listas horizontales y verticales.

## <a name="selectionselectionmd"></a>[Selección](selection.md)

De forma predeterminada, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) la selección está deshabilitada. Sin embargo, se puede habilitar la selección única o múltiple.

## <a name="empty-viewsemptyviewmd"></a>[Vistas vacías](emptyview.md)

En [ `CollectionView` ](xref:Xamarin.Forms.CollectionView), se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para su presentación. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrollingscrollingmd"></a>[Desplazarse](scrolling.md)

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que se muestran por completo los elementos. Además, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) define dos [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que se desplace mediante programación los elementos en la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado en la vista.
