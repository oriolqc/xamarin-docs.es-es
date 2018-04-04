---
title: Trabajar con tablas y las celdas
description: Mostrar datos con UITableView Xamarin.iOS
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: a1cda3632a75c7e462e763a34fdb5b586237b670
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-tables-and-cells"></a>Trabajar con tablas y las celdas


Esta sección presenta las clases utilizadas para crear y mostrar tablas, a continuación, proporciona ejemplos de cómo se utilizan en Xamarin.iOS. Se tratará con la apariencia predeterminada para las tablas, personalizar el diseño, implementación de la edición y usar el Diseñador de iOS de Xamarin para diseñar una tabla visualmente. A veces la presentación obviamente es una lista de filas (por ejemplo, la aplicación de música) y otras veces se difíciles de reconocer el control de tabla (como la modificación en la aplicación de contactos o una conversación en la aplicación de mensajes).

Personas que trabajan en aplicaciones multiplataforma con Xamarin.Android, el control de UITableView es similar a la clase de ListView en Android (y la clase UITableViewSource es similar a las clases de adaptador de Android).

Estos artículos tendrá una información completa sobre trabajar con tablas, incluidas:

-   **Tabla partes** : introducir y explicar los elementos visuales de la `UITableView` control. 
-   **Mostrar datos en tablas** – que muestra cómo crear y rellenar una tabla, use diferentes estilos de tabla y de celda y evitar problemas de memoria mediante el reciclado de objetos de la celda. 
-   **Uso avanzado de** : generar celdas personalizadas y utilizar las características de edición de la clase UITableView. 
-   **Crear una tabla visualmente** : uso del Diseñador de Xamarin para iOS para crear una interfaz controlado por tablas con un guión gráfico. 


## <a name="contents"></a>Contenido

 [Tabla partes &amp; funcionalidad](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Relleno de una tabla con datos](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Personalización de la apariencia de una tabla](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Edición](~/ios/user-interface/controls/tables/editing.md)
 
 [Acciones de fila](~/ios/user-interface/controls/tables/row-action.md)

 [Crear tablas en un guión gráfico.](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Ajuste del tamaño automático de la altura de la fila](~/ios/user-interface/controls/tables/autosizing-row-height.md)


## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [Tablas de guiones gráficos (ejemplo)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
- [Solo puede conducir al TableView de guión gráfico](https://developer.xamarin.com/recipes/ios/general/storyboard/storyboard_a_tableview)
- [Introducción a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Ejemplo de TableEditing en Github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Ejemplo de TableParts en Github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Ejemplo de TableAndCellStyles en Github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Referencia de clase UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Referencia de clase UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
