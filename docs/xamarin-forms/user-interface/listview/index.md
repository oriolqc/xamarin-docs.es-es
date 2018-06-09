---
title: Xamarin.Forms ListView
description: Esta guía presenta la vista de lista de Xamarin.Forms, que se puede usar para presentar datos en listas interactivas y atractivas.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: f73e7b70749a7a6913856d8c753db63a6d0a2bbe
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245006"
---
# <a name="xamarinforms-listview"></a>Xamarin.Forms ListView

ListView es una vista para presentar listas de datos, especialmente las listas largas que requieren desplazamiento. Esta guía le mostrará cómo usar ListView:

1. **[Orígenes de datos](data-and-databinding.md)**  &ndash; rellenar un control ListView con datos, con o sin enlace de datos.
2. **[Apariencia de la celda](customizing-cell-appearance.md)**  &ndash; personalizar la apariencia de las celdas integradas o crear su propia celda personalizado.
3. **[Lista apariencia](customizing-list-appearance.md)**  &ndash; personalizar la apariencia de ListView. Configurar los encabezados y pies de página, habilitar a los grupos y cambiar el alto de las filas.
4. **[Interactividad](interactivity.md)**  &ndash; controlar derivaciones y las selecciones, implementar Deslizar para actualizar y agregar acciones contextuales.
5. **[Rendimiento](performance.md)**  &ndash; evitar problemas de rendimiento.

## <a name="use-cases"></a>Casos de uso
Asegúrese de que ListView es el control adecuado para sus necesidades. ListView puede usarse en cualquier situación donde se muestran desplazables listas de datos. ListView admite acciones de contexto y enlace de datos.

ListView no debe confundirse con [TableView](~/xamarin-forms/user-interface/tableview.md). El control TableView constituye una mejor opción siempre que tenga una lista de no enlazadas a de opciones o datos. Por ejemplo, la aplicación de configuración de iOS, que tiene un conjunto predefinido de principalmente de opciones, es más adecuada usar TableView de ListView.

También tenga en cuenta que un control ListView es mejor ideal para datos homogéneos &ndash; es decir, todos los datos deben ser del mismo tipo. Esto es porque un solo tipo de celda puede utilizarse para cada fila de la lista. TableViews puede admitir varios tipos de celda, por lo que son una mejor opción cuando necesite una combinación de vistas.


## <a name="components"></a>Componentes
ListView tiene un número de componentes disponibles para probar la funcionalidad nativa de cada plataforma. Cada uno de estos componentes se describe a continuación:

- **[Encabezados y pies de página](customizing-list-appearance.md#Headers_and_Footers)**  &ndash; separar texto o vista para mostrar al principio y al final de una lista de datos de la lista. Encabezados y pies de página pueden enlazarse a un origen de datos independientemente del origen de datos de la vista de lista.
- **[Grupos de](customizing-list-appearance.md#Grouping)**  &ndash; datos en un control ListView se pueden agrupar para explorar más fácilmente. Normalmente, los grupos están enlazados a datos:

![](images/grouping-depth.png "ListView con los datos agrupados")

- **[Las celdas](customizing-cell-appearance.md)**  &ndash; datos en un control ListView se presentan en las celdas. Cada celda corresponde a una fila de datos. Hay celdas integradas que puede elegir, o puede definir su propia celda personalizado. Las celdas integradas y personalizadas pueden ser usa/definido en XAML o código.
  - **[Integrados](customizing-cell-appearance.md#Built_in_Cells)**  &ndash; integrada en las celdas, especialmente TextCell y ImageCell, puede ser grande para el rendimiento, puesto que corresponden a los controles nativos en cada plataforma.
       - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash; muestra una cadena de texto, opcionalmente con detalle. Texto de detalle se representa como una segunda línea en una fuente menor con un color de énfasis.
       - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash; muestra una imagen con texto. Aparece como una TextCell con una imagen de la izquierda.
  - **[Las celdas personalizadas](customizing-cell-appearance.md#customcells)**  &ndash; celdas personalizado son excelentes cuando se necesita para presentar datos complejos. Por ejemplo, una vista personalizada puede usarse para presentar una lista de canciones, incluido el álbum y del intérprete:

![](images/image-cell-default.png "ListView con ImageCells")

Para más información acerca de cómo personalizar las celdas de un control ListView, consulte [personalizar la apariencia de celdas de ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funcionalidad
ListView admite una serie de estilos de interacción, incluidos:

- **[Actualización de extracción](interactivity.md#Pull_to_Refresh)**  &ndash; ListView admite la actualización de extracción en cada plataforma.
- **[Acciones de contexto](interactivity.md#Context_Actions)**  &ndash; ListView admite realizar acciones en los elementos individuales de una lista. Por ejemplo, puede implementar la acción de deslizar rápidamente en iOS, o mantenga el puntero de acciones en Android.
- **[Selección](interactivity.md#selectiontaps)**  &ndash; puede escuchar las selecciones y deselections para realizar una acción cuando una fila que se derivan.

![](images/context-default.png "ListView con acciones de contexto")

Para obtener más información sobre las características de interactividad de ListView, consulte [acciones & interactividad con ListView](interactivity.md).


## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con ListView (ejemplo)](https://developer.xamarin.com/samples/WorkingWithListview)
- [Dos forma enlace (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [Compila en celdas (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Celdas personalizadas (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Agrupación (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [Vista personalizada de representador (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/WorkingWithListviewNative)
- [ListView interactividad (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [Libro de iOS](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-ios.workbook)
- [Libro de Android](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-android.workbook)
