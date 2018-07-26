---
title: Trabajar con tablas y las celdas de Xamarin.iOS
description: Este documento incluye vínculos a diversas guías que describen cómo mostrar los datos con el control de UITableView en una aplicación de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: ea5b6ba532d577bd503529065eef803acf3a7aa9
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241703"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Trabajar con tablas y las celdas de Xamarin.iOS

Esta sección presenta las clases utilizadas para crear y mostrar tablas, a continuación, proporciona ejemplos de cómo se usan en Xamarin.iOS. Tratará con la apariencia predeterminada para las tablas, personalizar el diseño, implementación de la edición y utilizando el Diseñador de iOS de Xamarin para diseñar una tabla visualmente. A veces la presentación es obviamente una lista de filas (por ejemplo, la aplicación de música) y otras veces es difícil para que reconozca el control de tabla (como la modificación de la aplicación de contactos, o una conversación en la aplicación de mensajes).

Para aquellos que trabajan en aplicaciones multiplataforma con Xamarin.Android, el control de UITableView es similar a la clase ListView en Android (y la clase UITableViewSource es similar a las clases de adaptador de Android).

Estos artículos tardará una visión completa de trabajar con tablas, incluidas:

-   **Partes de la tabla** : presentación y explicación de los elementos visuales de la `UITableView` control. 
-   **Mostrar datos en tablas** – que demuestra cómo crear y rellenar una tabla, utilizar diferentes estilos de tabla y de celda y evitar problemas de memoria mediante el reciclado de objetos de celda. 
-   **Uso avanzado** : crear celdas personalizadas y usar las características de edición de la clase UITableView. 
-   **Creación de una tabla visualmente** : uso del Diseñador de Xamarin para iOS para crear una interfaz controlado por tablas con un guión gráfico. 

## <a name="contents"></a>Contenido

 [Partes de la tabla &amp; funcionalidad](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Relleno de una tabla con datos](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalización de la apariencia de una tabla](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Edición](~/ios/user-interface/controls/tables/editing.md)
 
 [Acciones de fila](~/ios/user-interface/controls/tables/row-action.md)

 [Creación de tablas en un guión gráfico](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Ajuste del tamaño automático de la altura de la fila](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [Tablas de guiones gráficos (ejemplo)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Guión gráfico una receta TableView](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introducción a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Ejemplo de TableEditing en Github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Ejemplo de TableParts en Github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Ejemplo de TableAndCellStyles en Github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Referencia de clase de UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Referencia de clase UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
