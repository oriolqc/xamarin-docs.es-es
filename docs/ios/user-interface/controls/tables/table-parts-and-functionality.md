---
title: Elementos de la tabla y la funcionalidad de Xamarin.iOS
description: Este documento describen las distintas partes de un UITableView de iOS. Describe los encabezados de sección, las celdas, pies de página de sección, el índice y modo de edición.
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c4d788cce12a9aabdd1170cd1a52915f3b30285f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200523"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>Elementos de la tabla y la funcionalidad de Xamarin.iOS

Un UITableView puede tener un estilo 'agrupado' o 'normal' y consta de las siguientes partes:

-  [Encabezado de sección](#Section_Header)
-  [Las celdas](#Cells) (o filas, si lo prefiere)
-  [Pie de página de sección](#Section_Footer)
-  [Index](#Index)
-  [Modo de edición](#Edit_Features) (incluye 'Deslice el dedo para eliminar' y arrastre los controladores para cambiar el orden de fila) 

Estas capturas de pantalla muestran cómo se muestran las filas de la sección, encabezados, pies de página, los controles de edición y el índice.

 [![](table-parts-and-functionality-images/image1a.png "Estas capturas de pantalla muestran cómo se muestran las filas de la sección, encabezados, pies de página, los controles de edición y el índice")](table-parts-and-functionality-images/image1a.png#lightbox)

Estas partes se describen más detalladamente a continuación:

<a name="Section_Header" />

## <a name="section-header"></a>Encabezado de sección

Celdas, opcionalmente, se agrupan en secciones, etiquetadas con un encabezado personalizado o con la etiqueta con un pie de página. Se puede establecer el encabezado con un valor de cadena o puede proporcionar una vista personalizada para permitir un diseño diferente o un estilo.

<a name="Cells" />

## <a name="cells"></a>Celdas

Las celdas son el elemento de interfaz de usuario principal de una tabla. Cuando se implementa correctamente, las celdas se reutilizan para mejorar la eficacia de memoria. Existen cuatro estilos de celda integrados, y puede crear sus propias celdas personalizadas: en el código, o en el diseñador al usar guiones gráficos.

<a name="Section_Footer"/>

## <a name="section-footer"></a>Pie de página de sección

El pie de página de la sección opcional se puede establecer con un valor de cadena, o puede proporcionar una vista personalizada para permitir un diseño diferente o un estilo. Encabezados y pies de página pueden establecerse de forma independiente.

<a name="Index" />

## <a name="index"></a>Índice

El índice aparece como una tira de caracteres hacia abajo el borde derecho de la tabla.
Sillones quede total o arrastrar en el índice acelera el desplazamiento a la parte de la tabla. Un índice es opcional pero se recomienda para ayudar a navegar por las listas largas. Normalmente, no se usa un índice con el estilo agrupadas.

<a name="Edit_Features" />

## <a name="editing-mode"></a>Modo de edición

Hay un par de diferentes características de edición:

- Deslice el dedo para eliminar celdas individuales.
- Entrando en modo de edición para mostrar los botones Eliminar en cada fila 
- Entrando en modo de edición para revelar los identificadores de volver a ordenar. 
- Insertar nuevas celdas (con animación).

El resto de este documento muestra cómo implementar todas estas características de UITableView con Xamarin.iOS.


## <a name="classes-overview"></a>Información general de clases

Aquí se muestran las clases primarias utilizadas para mostrar las vistas de tabla:

[![](table-parts-and-functionality-images/classdiagram.png "Aquí se muestran las clases primarias utilizadas para mostrar las vistas de tabla")](table-parts-and-functionality-images/classdiagram.png#lightbox)

El propósito de cada clase se describe a continuación:

- **UITableView** : una vista que contiene una colección de celdas dentro de un contenedor desplazable. La vista de tabla normalmente usa toda la pantalla en una aplicación de iPhone, pero puede existir como parte de una vista más grande en el iPad (o aparecen en un elemento flotante). 
- **UITableViewCell** : una vista que representa una única celda (o fila) en una vista de tabla. Hay cuatro tipos de celda integrados y es posible crear personalizadas celdas ambos en C# o con el Diseñador de iOS. 
- **UITableViewSource** : Xamarin.iOS exclusivo clase abstracta que proporciona todos los métodos necesarios para mostrar una tabla, incluido el recuento de filas, devolviendo una vista de la celda para cada fila, control de selección de filas y muchas otras características opcionales. Le *debe* subclase esta lista para obtener un funcionamiento UITableView. 
- **NSIndexPath** : sección y la fila contiene propiedades que identifican la posición de una celda en una tabla. 
- **UITableViewController** – un UIViewController listos para usar con un codificado de forma rígida UITableView como su vista y accesible a través de la propiedad TableView. 
- **UIViewController** : si la tabla no ocupe toda la pantalla, puede agregar un UITableView a cualquier UIViewController con su marco se establezca correctamente. 

UITableViewSource reemplaza las dos clases siguientes, que siguen estando disponibles en Xamarin.iOS, pero no son normalmente necesarios:

- **UITableViewDataSource** : Objective-C un protocolo que se modela en Xamarin.iOS como una clase abstracta. Debe ser una subclase para proporcionar una tabla con una vista para cada celda, así como información acerca de los encabezados, pies de página y el número de filas y secciones de la tabla. 
- **UITableViewDelegate** : Objective-C un protocolo que se modela en Xamarin.iOS como una clase. Controla la selección, edición de las características y otras características opcionales de la tabla. 

En este documento los ejemplos usan UITableViewSource y pasar por alto estas dos clases. Se mencionan aquí porque los ejemplos de Objective-C que se encuentra en la documentación de Apple hará referencia a ellas, por lo que es útil para comprender qué hacen (y que puede usar UITableViewSource de Xamarin.iOS en su lugar).

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
