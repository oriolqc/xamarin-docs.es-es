---
title: Representadores rápidos de Xamarin.Forms
description: Este artículo presentan a los representadores rápidos, que reducen la inflación y los costos de representación de un control de Xamarin.Forms en Android mediante la reducción de la jerarquía de control nativo resultante.
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 861d9e3f898dcd61015d9aca27ae66c3fe72d1a9
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970719"
---
# <a name="xamarinforms-fast-renderers"></a>Representadores rápidos de Xamarin.Forms

Tradicionalmente, la mayoría de los representadores de control original en Android se compone de dos vistas:

- Control nativo, como un `Button` o `TextView`.
- Un contenedor `ViewGroup` que controla algunas de las otras tareas, administración de gestos y el trabajo de diseño.

Sin embargo, este enfoque tiene una implicación del rendimiento que dos vistas se crean para cada control lógico, lo que resulta en un árbol visual más compleja que requiere más memoria y más capacidad de procesamiento para representar en la pantalla.

Los representadores rápidos reducen la inflación y los costos de representación de un control de Xamarin.Forms en una sola vista. Por lo tanto, en lugar de crear dos vistas y agregarlos al árbol de la vista, se crea uno solo. Esto mejora el rendimiento porque crea menos objetos, lo que significa que a su vez un árbol de vista menos complejo, y menos uso de memoria (que también tiene como resultado menos pausas de la colección de elementos no utilizados).

Representadores rápidos están disponibles para los siguientes controles de Xamarin.Forms en Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

Funcionalmente, estos representadores rápidos no son distintos a los representadores heredados. Xamarin.Forms 4.0 y versiones posteriores, todas las aplicaciones destinadas a `FormsAppCompatActivity` usará estos representadores rápidos de forma predeterminada. Los representadores para todos los controles nuevos, incluidos [ `ImageButton` ](xref:Xamarin.Forms.ImageButton) y [ `CollectionView` ](xref:Xamarin.Forms.CollectionView), utilice el enfoque de representador rápida.

Mejoras de rendimiento al usar a representadores rápidos variarán para cada aplicación, en función de la complejidad del diseño. Por ejemplo, son posibles mejoras de rendimiento de x2 al desplazarse a través de un [ `ListView` ](xref:Xamarin.Forms.ListView) que contienen miles de filas de datos, donde se realizan las celdas de cada fila de controles que usar representadores rápidos, que da como resultado visiblemente desplazamiento más suave.

> [!NOTE]
> Los representadores personalizados pueden crearse para representadores rápidos utilizando el mismo enfoque que se usan para los representadores heredados. Para obtener más información, consulte [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Representadores personalizados).

## <a name="backwards-compatibility"></a>Compatibilidad con versiones anteriores

Representadores rápidos pueden reemplazarse con los métodos siguientes:

1. Habilitación de los representadores heredados agregando la siguiente línea de código para su `MainActivity` clase antes de llamar a `Forms.Init`:

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. Uso de los representadores personalizados que tienen como destino a los representadores heredados. Los representadores personalizados existentes seguirán funcionando con los representadores heredados.
1. Especificar otro `View.Visual`, tales como `Material`, que usa distintos representadores. Para obtener más información acerca de Material Visual, consulte [Xamarin.Forms Material Visual](~/xamarin-forms/user-interface/visual/material-visual.md).

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
