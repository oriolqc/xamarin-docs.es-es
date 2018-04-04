---
title: Actualizaciones de diseño Visual
description: Explorar las nuevas características de iOS 11
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 50858592ec6246fe825e80a3039f3640ebf708a9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="visual-design-updates"></a>Actualizaciones de diseño Visual

_Explorar las nuevas características de iOS 11_

En iOS 11, Apple incorporó nuevos cambios visuales, incluidas las actualizaciones en la barra de navegación, barra de búsqueda y vistas de la tabla. Además, se realizaron mejoras para proporcionar mayor flexibilidad a través de los márgenes y el contenido de pantalla completa. Estos cambios se tratan en esta guía.

## <a name="uikit"></a>UIKit

Barras de UIKit se han adaptado en iOS 11 para hacerlos más accesibles para los usuarios finales.

Uno de estos cambios es una nueva pantalla de HUD que aparece cuando un usuario long-presiona en una barra de elemento. Para habilitar esto, establezca el `largeContentSizeImage` propiedad `UIBarItem` y agregar una imagen más grande a través de un [catálogo de activos](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barra de navegación
iOS 11 introdujo nuevas funciones para facilitar la lectura de los títulos de la barra de navegación. Las aplicaciones pueden mostrar este título mayor mediante la asignación de la `PrefersLargeTitles` propiedad en true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Títulos de mayor valor de la aplicación hace _todos los_ títulos de las barras de navegación a través de la aplicación aparezcan más grandes, como se muestra en la siguiente captura de pantalla:

![Título de navegación de gran tamaño](visual-design-images/image7.png)

Para controlar cuándo se muestra un título grande en una barra de navegación, establezca la `LargeTitleDisplayMode` en el elemento de navegación para `Always`, `Never`, o `Automatic`.

### <a name="search-controller"></a>Controlador de búsqueda

iOS 11 hizo más fácil de agregar un controlador de búsqueda directamente a la barra de navegación. Una vez haya creado un controlador de búsqueda, puede agregarlo a la barra de navegación con el `SearchController` propiedad:

```csharp
NavigationItem.SearchController = searchController;
```

[![Título de navegación grandes con la barra de búsqueda](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

Dependiendo de la funcionalidad de la aplicación, puede o sin necesidad de la barra de búsqueda para ocultar cuando un usuario se desplaza a través de una lista. Se puede ajustar mediante el `HidesSearchBarWhenScrolling` propiedad.

## <a name="margins"></a>Márgenes

Apple ha creado una nueva propiedad – `directionalLayoutMargins` : que se puede utilizar para establecer el espacio entre las vistas y subvistas. Use `directionalLayoutMargins` con `leading` o `trailing` márgenes. Independientemente de si el sistema es un lenguaje de izquierda a derecha o de derecha a izquierda, el espaciado de la aplicación se establece correctamente por iOS.

En iOS 10 y antes de, todas las vistas tenían un tamaño mínimo de margen que podría alinear. iOS 11 introdujo la opción para invalidar ese mediante `ViewRespectsSystemMinimumLayoutMargins`. Por ejemplo, si establece esta propiedad en false permite ajustar los márgenes de borde en cero:

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![Margen de que se muestra la imagen con cero bajorrelieve en ios 11](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>Contenido de pantalla completa

iOS 7 [introdujo](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` y `bottomLayoutGuide` como una manera de restringir las vistas de modo que no están ocultos mediante barras UIKit y están en un área visible de la pantalla. Se han quedado obsoletas en iOS 11 en favor de la _zona segura de_.

La zona segura es una nueva manera de pensar en el espacio de la aplicación y cómo se agregan restricciones entre una vista y una vista super visible. Por ejemplo, considere la siguiente imagen:

[![Parte superior del área segura vs y Guía de diseño de la parte inferior](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

Anteriormente, si agrega una vista y desea que esté visible en el área de color verde anterior, se restringen a la _inferior_ de la `TopLayoutGuide` y el _arriba_ de la `BottomLayoutGuide`. En iOS 11, debería restringir en su lugar a la _arriba_ y la _inferior_ de la zona segura. A continuación se muestra un ejemplo:

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Vista de tabla

El UITableView ha tenido un número de cambios pequeñas pero importantes, en iOS 11.

De forma predeterminada, los encabezados y pies de página, las celdas son ahora ajustar de forma automática basándose en su contenido. Para no participar en este conjunto de comportamiento de ajuste automático del tamaño del `EstimatedRowHeight`, `EstimatedSectionHeaderHeight`, o `EstimatedSectionFooterHeight` a cero.

Sin embargo, en algunos casos (como cuando Agregar UITableViewController en el Diseñador de iOS o cuando se usa Storboards existente en el generador de interfaz) puede que necesite habilitar manualmente las celdas autoajustables. Para ello, asegúrese de que ha establecido las siguientes propiedades en la vista de tabla de las celdas, los encabezados y pies de página, respectivamente:

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

iOS 11 ha ampliado la funcionalidad de las acciones de la fila. `UISwipeActionsConfiguration` se introdujo para definir un conjunto de acciones que debe tener lugar cuando el usuario lectores de crédito en cualquier dirección en una fila de una vista de tabla. Este comportamiento es similar de la aplicación Mail.app nativo. Para obtener más información, consulte el [acciones de fila](~/ios/user-interface/controls/tables/row-action.md) guía.

Vistas de la tabla tienen compatibilidad con arrastrar y colocar en iOS 11. Para obtener más información, consulte el [arrastrar y colocar](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) guía.


## <a name="related-links"></a>Vínculos relacionados

- [What's New en iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto actualizada tienda de aplicaciones (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualizar la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
