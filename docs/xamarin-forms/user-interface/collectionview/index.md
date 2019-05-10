---
title: Xamarin.Forms CollectionView
description: El elemento CollectionView es una vista flexible y eficaz para presentar las listas de datos con las especificaciones de diseño diferente.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a6cb6e695a4c19627b183060a7636320f8083ee2
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65048142"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

## <a name="introductionintroductionmd"></a>[Introducción](introduction.md)

El `CollectionView` es una vista flexible y eficaz para presentar las listas de datos con las especificaciones de diseño diferente.

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

Un `CollectionView` se rellena con datos estableciendo sus `ItemsSource` propiedad a cualquier colección que implementa `IEnumerable`. Se puede definir la apariencia de cada elemento de la lista estableciendo la `ItemTemplate` propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Diseño](layout.md)

De forma predeterminada, un `CollectionView` mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar las cuadrículas y listas horizontales y verticales.

## <a name="selectionselectionmd"></a>[Selección](selection.md)

De forma predeterminada, `CollectionView` la selección está deshabilitada. Sin embargo, se puede habilitar la selección única o múltiple.

## <a name="empty-viewsemptyviewmd"></a>[Vistas vacías](emptyview.md)

En `CollectionView`, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para su presentación. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scrollingscrollingmd"></a>[Desplazarse](scrolling.md)

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que se muestran por completo los elementos. Además, `CollectionView` define dos `ScrollTo` métodos, que se desplace mediante programación los elementos en la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado en la vista.
