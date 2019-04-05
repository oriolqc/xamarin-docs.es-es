---
title: Xamarin.Forms CollectionView
description: El elemento CollectionView es una vista flexible y eficaz para presentar las listas de datos con las especificaciones de diseño diferente.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: b22449659d2d4b7791328d53ed2d2b29d405ffc8
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58330040"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![Vista previa](~/media/shared/preview.png)

> [!IMPORTANT]
> El `CollectionView` es actualmente una versión preliminar y carece de algunas de sus funciones previstas. Además, la API puede cambiar cuando se completa la implementación.

`CollectionView` es una vista para presentar las listas de datos mediante las especificaciones de diseño diferente. Su objetivo es proporcionar una forma más flexible y una alternativa de alto rendimiento [ `ListView` ](xref:Xamarin.Forms.ListView). Mientras el `CollectionView` y `ListView` API son similares, existen algunas diferencias importantes:

- `CollectionView` tiene un modelo de diseño flexible que permite que los datos se presentan vertical u horizontalmente, en una lista o una cuadrícula.
- `CollectionView` no tiene ningún concepto de celdas. En su lugar, se usa una plantilla de datos para definir la apariencia de cada elemento de datos en la lista.
- `CollectionView` usa automáticamente la virtualización proporcionada por los controles nativos subyacentes.
- `CollectionView` reduce la superficie de API de [ `ListView` ](xref:Xamarin.Forms.ListView). Muchas propiedades y eventos de `ListView` no están presentes en `CollectionView`.
- `CollectionView` no incluye separadores integrados.

`CollectionView` está disponible en las versiones preliminares de Xamarin.Forms 4.0. Sin embargo, es experimental actualmente y solo se puede usar agregando la siguiente línea de código para su `AppDelegate` clase en iOS, así como su `MainActivity` clase en Android, antes de llamar a `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!NOTE]
> `CollectionView` solo está disponible en iOS y Android.

## <a name="populate-collectionview-with-datapopulate-datamd"></a>[Rellenar CollectionView con datos](populate-data.md)

Un `CollectionView` se rellena con datos estableciendo sus `ItemsSource` propiedad a cualquier colección que implementa `IEnumerable`. Se puede definir la apariencia de cada elemento de la lista estableciendo la `ItemTemplate` propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="specify-collectionview-layoutlayoutmd"></a>[Especificar el diseño de CollectionView](layout.md)

De forma predeterminada, un `CollectionView` mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar las cuadrículas y listas horizontales y verticales.

## <a name="set-collectionview-selection-modeselectionmd"></a>[Establecer el modo de selección de CollectionView](selection.md)

De forma predeterminada, `CollectionView` la selección está deshabilitada. Sin embargo, se puede habilitar la selección única o múltiple.

## <a name="display-an-emptyview-when-data-is-unavailableemptyviewmd"></a>[Mostrar un EmptyView cuando los datos no están disponibles](emptyview.md)

En `CollectionView`, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para su presentación. La vista vacía puede ser una cadena, una vista o varias vistas.

## <a name="scroll-an-item-into-viewscrollingmd"></a>[Desplazar un elemento en la vista](scrolling.md)

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que se muestran por completo los elementos. Además, `CollectionView` define dos `ScrollTo` métodos, que se desplace mediante programación los elementos en la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado en la vista.
