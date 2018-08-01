---
title: Control de menú (Force Touch) en Xamarin watchOS
description: Este documento describe cómo usar el gesto de táctil watchOS force en Xamarin. Explica cómo responder a un toque force, cómo agregar un menú y cambiar los elementos de menú.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4b973b925b99189416087224644c376864c56871
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791350"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Control de menú (Force Touch) en Xamarin watchOS

Kit de inspección proporciona un gesto Force Touch que activa un menú cuando se implementa en una pantalla de aplicación de inspección.

![](menu-images/menu.png "Apple Watch que muestra un menú")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Responder a la entrada táctil Force

Si un `Menu` se ha implementado para un controlador de interfaz, cuando un usuario realiza un toque Force se mostrará el menú. Si no se ha implementado ningún menú, la pantalla se anima brevemente se produce un ninguna otra acción.

Forzar los últimos retoques no están asociados a ningún elemento determinado en la pantalla; solo menú se pueden adjuntar a un controlador de interfaz y aparecerá con independencia de dónde se produce la acción de presionar Force táctil en la pantalla.

Entre uno y cuatro menú se pueden presentar opciones.


## <a name="adding-a-menu"></a>Agregar un menú

A `Menu` deben agregarse a un `InterfaceController` en el guión gráfico en tiempo de diseño. Cuando se arrastra un control de menú a un controlador de interfaz no hay ninguna indicación visual en la vista previa del guión gráfico, pero la **menú** aparece en la **esquema del documento** panel:

![](menu-images/menu-action.png "Edición de un menú en tiempo de diseño")

Hasta cuatro menú elementos se pueden agregar al control de menú. Se pueden configurar en el **propiedades** panel. Se pueden establecer los siguientes atributos:

- Título, y
- Imagen personalizada, o
- Una imagen de sistema: Aceptar, agregar, bloque, rechazo, Info, quizás, más, silenciar, pausar, reproducir, repita, reanudar, recurso compartido, orden aleatorio, altavoces, Papelera.

Crear un `Action` seleccionando el **eventos** sección de la **propiedades** controlador y escriba el nombre para el método de acción. Se creará un método parcial en el código, que se puede implementar en la clase de controlador de interfaz, similar al siguiente:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Imágenes personalizadas

Similar a imágenes de ficha en iOS, imágenes de elemento de menú requieren un patrón opaco con un canal alfa que permite que el fondo mostrar a través de.

Debe agregar las imágenes utilizadas para el menú para el proyecto de aplicación de inspección (no el inspección extensión proyecto de aplicación) para mejorar el rendimiento.


## <a name="changing-the-menu-items"></a>Cambiar los elementos de menú

<!--
### Design Time Items

Menu items added the the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Agregar en tiempo de ejecución

No se puede producir un `Menu` va a agregar a un controlador de interfaz en tiempo de ejecución, aunque la colección de `MenuItem`s *puede* modificarse mediante programación.
Use la `AddMenuItem` método tal como se muestra:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

La API de Kit de inspección de Xamarin.iOS actualmente requiere un `selector` para el `AdMenuItem` método, que se debe declarar como esta:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Quitar en tiempo de ejecución

El `ClearAllMenuItems` método puede llamarse para todos los quitar *agregado mediante programación* elementos de menú.

No se puede borrar los elementos de menú que se configura en el guión gráfico.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de menú de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
