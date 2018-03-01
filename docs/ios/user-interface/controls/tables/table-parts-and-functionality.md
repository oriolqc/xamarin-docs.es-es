---
title: Funcionalidad y los elementos de tabla
ms.topic: article
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 1a47cad858e4b0b362da18ebe58142ade2574be2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="table-parts-and-functionality"></a>Funcionalidad y los elementos de tabla

Un UITableView puede tener un estilo 'agrupado' o 'normal' y consta de las siguientes partes:

-  [Encabezado de sección](#Section_Header)
-  [Las celdas](#Cells) (o filas, si lo prefiere)
-  [Pie de página de sección](#Section_Footer)
-  [Índice](#Index)
-  [Modo de edición](#Edit_Features) (incluye 'Deslice el dedo para eliminar' y arrastre los controladores para cambiar el orden de las filas) 


Estas capturas de pantalla muestran cómo se muestran las filas de la sección, encabezados, pies de página, controles de edición y el índice.

 [ ![](table-parts-and-functionality-images/image1a.png "Estas capturas de pantalla muestran cómo se muestran las filas de la sección, encabezados, pies de página, controles de edición y el índice")](table-parts-and-functionality-images/image1a.png)

Estos elementos se describen con más detalle a continuación:

 <a name="Section_Header" />


## <a name="section-header"></a>Encabezado de sección

Celdas, opcionalmente, puede agrupadas en secciones, una etiqueta con un encabezado personalizado o una etiqueta con un pie de página. El encabezado puede establecerse con un valor de cadena o se puede proporcionar una vista personalizada para que permita un diseño diferente o estilo.

 <a name="Cells" />


## <a name="cells"></a>Celdas

Las celdas son el elemento de la interfaz de usuario principal de una tabla. Cuando se implementa correctamente, las celdas se reutilizan para la eficacia de la memoria. Hay cuatro estilos de celda integrados, y puede crear sus propios celdas personalizados: en el código, o en el diseñador al usar guiones gráficos.


## <a name="section-footer"></a>Pie de página de sección

El pie de página de la sección opcional se puede establecer con un valor de cadena, o se puede proporcionar una vista personalizada para que permita un diseño diferente o estilo. Encabezados y pies de página se pueden establecer independientemente.

 <a name="Index" />


## <a name="index"></a>Índice

El índice aparece como una tira de caracteres hacia abajo el borde derecho de la tabla.
Tocar o bien arrastrando en el índice acelera el desplazamiento a la parte de la tabla. Un índice es opcional pero se recomienda para ayudar a navegar listas largas. Normalmente, no se utiliza un índice con el estilo de agrupadas.

 <a name="Edit_Features" />


## <a name="editing-mode"></a>Modo de edición

Hay un par de diversas características de edición disponibles:

-  Deslice el dedo para eliminar celdas individuales.
-  Entrando en modo de edición para mostrar botones de eliminación en cada fila 
-  Entrando en modo de edición para revelar los identificadores de volver a ordenar. 
-  Insertar nuevas celdas (con animación).


El resto de este documento muestra cómo implementar todas estas características UITableView con Xamarin.iOS.

 <a name="Classes_Overview" />


## <a name="classes-overview"></a>Información general de clases

Aquí se muestran las clases principales utilizadas para mostrar las vistas de tabla:

 [ ![](table-parts-and-functionality-images/classdiagram.png "Aquí se muestran las clases principales utilizadas para mostrar las vistas de tabla")](table-parts-and-functionality-images/classdiagram.png)

El propósito de cada clase se describe a continuación:

-   **UITableView** : una vista que contiene una colección de celdas dentro de un contenedor desplazable. La vista de tabla normalmente utiliza toda la pantalla en una aplicación de iPhone pero puede existir como parte de una vista más grande en el iPad (o aparecen en una popover). 
-   **UITableViewCell** : una vista que representa una única celda (o fila) en una vista de tabla. Hay cuatro tipos de celda integrados y es posible crear celdas personalizadas en C# o con el Diseñador de iOS. 
-   **UITableViewSource** : Xamarin.iOS exclusivo clase abstracta que proporciona todos los métodos necesarios para mostrar una tabla, incluido el recuento de filas, devolver una vista de celda para cada fila, control de selección de filas y muchas otras características opcionales. Se *debe* subclase esta opción para obtener una UITableView en funcionamiento. 
-   **NSIndexPath** : filas de Contains y la sección propiedades que identifican la posición de una celda de una tabla. 
-   **UITableViewController** – un UIViewController listos para usar que tiene un codificado de forma rígida UITableView como su vista y accesible a través de la propiedad TableView. 
-   **UIViewController** : si la tabla no ocupa toda la pantalla, puede agregar un UITableView a cualquier UIViewController con su marco se establezca correctamente. 


UITableViewSource reemplaza las dos clases siguientes, que siguen estando disponibles en Xamarin.iOS, pero no son normalmente necesarios:

-   **UITableViewDataSource** : protocolo de un objetivo-C que se modela en Xamarin.iOS como una clase abstracta. Debe ser una subclase para proporcionar una tabla con una vista para cada celda, así como información sobre encabezados, pies de página y el número de filas y secciones de la tabla. 
-   **UITableViewDelegate** : protocolo de un objetivo-C que se modela en Xamarin.iOS como una clase. Controla la selección, edición de características y otras características opcionales de la tabla. 


En este documento, los ejemplos usan UITableViewSource y pasar por alto estas dos clases. Que se mencionan aquí porque ejemplos Objective-C que se encuentra en la documentación de Apple hará referencia a ellos, por lo que resulta útil para comprender qué hacen (y que puede usar en su lugar UITableViewSource de Xamarin.iOS).


## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
