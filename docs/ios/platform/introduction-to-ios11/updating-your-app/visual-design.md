---
title: Actualizaciones de diseño visuales en iOS 11
description: Este documento describe el diseño visual actualizaciones introdujeron en iOS 11. Describe los cambios realizados en las barras de navegación, los controladores de búsqueda, los márgenes, contenido de pantalla completa y vistas de tabla.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c6351f2c25f8e31181c761aea1b471315a8a05e8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400220"
---
# <a name="visual-design-updates-in-ios-11"></a>Actualizaciones de diseño visuales en iOS 11

En iOS 11, Apple introdujo nuevos cambios visuales, incluidas las actualizaciones de la barra de navegación, barra de búsqueda y las vistas de tabla. Además se han realizado mejoras para permitir una mayor flexibilidad a través de los márgenes y el contenido de pantalla completa. Estos cambios se tratan en esta guía. 

Para obtener información específicamente sobre el diseño para iPhone X, vea Apple [diseñar para iPhone X](https://developer.apple.com/videos/play/fall2017/801/) vídeo.

## <a name="uikit"></a>UIKit

Se han adaptado los barras UIKit en iOS 11 para que sean más accesibles para los usuarios finales.

Uno de los cambios es una nueva pantalla de HUD que aparece cuando presiones de largo de un usuario en una barra de elemento. Para habilitar esto, establezca el `largeContentSizeImage` propiedad `UIBarItem` y agregar una imagen más grande a través de un [catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra de navegación
iOS 11 introdujo nuevas funcionalidades para facilitar la lectura de los títulos de la barra de navegación. Las aplicaciones pueden mostrar este título más grande, se asigna el `PrefersLargeTitles` propiedad en true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Hace que los títulos más grandes de configuración en la aplicación _todas_ títulos de las barras de navegación a través de la aplicación parezcan más grandes, como se muestra en la siguiente captura de pantalla:

![Título de navegación de gran tamaño](visual-design-images/image7.png)

Para controlar cuándo se muestra un título de gran tamaño en una barra de navegación, establezca la `LargeTitleDisplayMode` en el elemento de navegación para `Always`, `Never`, o `Automatic`.

### <a name="search-controller"></a>Controlador de búsqueda

iOS 11 ha facilitado a agregar un controlador de búsqueda directamente a la barra de navegación. Una vez haya creado un controlador de búsqueda, puede agregarlo a la barra de navegación con el `SearchController` propiedad:

```csharp
NavigationItem.SearchController = searchController;
```

[![Título de navegación de gran tamaño con la barra de búsqueda](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

Dependiendo de la funcionalidad de la aplicación, puede o no se puede ocultar cuando un usuario se desplaza a través de una lista de la barra de búsqueda. Se puede ajustar mediante el `HidesSearchBarWhenScrolling` propiedad.

## <a name="margins"></a>Márgenes

Apple ha creado una nueva propiedad: `directionalLayoutMargins` : que se puede utilizar para establecer el espacio entre las vistas y subvistas. Use `directionalLayoutMargins` con `leading` o `trailing` márgenes. Independientemente de si el sistema es un lenguaje de izquierda a derecha o de derecha a izquierda, el espaciado en la aplicación se establezca correctamente por iOS.

En iOS 10 y antes de, todas las vistas tenían un tamaño mínimo de margen a la que estaría en consonancia. iOS 11 introdujo la opción para invalidar ese mediante `ViewRespectsSystemMinimumLayoutMargins`. Por ejemplo, si establece esta propiedad en false permite ajustar los márgenes de edge en cero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![Margen de imagen que muestra con cero bajorrelieve en ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Contenido de pantalla completa

iOS 7 [introdujo](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` y `bottomLayoutGuide` como una manera de restringir las vistas para que no estén ocultos por barras UIKit y están en un área visible de la pantalla. Estos han quedado obsoletas en iOS 11 en favor de la _área segura_.

El área segura es una nueva forma de pensar acerca del espacio visible de la aplicación y cómo se agregan restricciones entre una vista y una vista super. Por ejemplo, considere la siguiente imagen:

[![Parte superior del área segura vs y Guía de diseño inferior](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

Anteriormente, si se ha agregado una vista y quería que sea visible en el área de color verde anterior, podría restringir a la _inferior_ de la `TopLayoutGuide` y _superior_ de la `BottomLayoutGuide`. En iOS 11, podría restringir en su lugar a la _superior_ y _inferior_ de la zona segura. A continuación se muestra un ejemplo:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Vista de tabla

El UITableView ha tenido una serie de cambios pequeñas pero importantes, en iOS 11.

De forma predeterminada, los encabezados, pies de página y celdas ahora ajusta automáticamente el tamaño en función de su contenido. Para dejar de participar en este conjunto de comportamiento de ajuste automático de tamaño el `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`, o `EstimatedSectionFooterHeight` a cero.

Sin embargo, en algunas circunstancias (por ejemplo, cuando Agregar UITableViewController en el Diseñador de iOS o al usar Storboards existente en Interface Builder) deberá habilitar manualmente las celdas autoajustables. Para ello, asegúrese de que ha establecido las siguientes propiedades en la vista de tabla para las celdas, encabezados y pies de página, respectivamente:

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 amplió la funcionalidad de las acciones de fila. `UISwipeActionsConfiguration` se introdujo para definir un conjunto de acciones que debe tener lugar cuando el usuario pase el dedo en cualquier dirección en una fila en una vista de tabla. Este comportamiento es similar de la aplicación Mail.app nativo. Para obtener más información, consulte el [acciones de fila](~/ios/user-interface/controls/tables/row-action.md) guía.

Vistas de tabla tienen compatibilidad con arrastrar y colocar en iOS 11. Para obtener más información, consulte el [arrastrar y colocar](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) guía.


## <a name="related-links"></a>Vínculos relacionados

- [Novedades en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada Store App (Apple)](https://developer.apple.com/app-store/product-page/)
- [Diseñar para iPhone X (Apple) (vídeo)](https://developer.apple.com/videos/play/fall2017/801/)
- [Actualización de la aplicación para iOS 11 (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)

