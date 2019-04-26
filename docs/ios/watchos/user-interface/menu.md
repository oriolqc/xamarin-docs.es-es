---
title: Control de menú (Force Touch) en Xamarin watchOS
description: Este documento describe cómo usar el gesto de watchOS force touch en Xamarin. Describe cómo responder a un toque de fuerza, cómo agregar un menú y cambiar los elementos de menú.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7696c820ab6fdf19bdef46db31061fb5914e6cf4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880698"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Control de menú (Force Touch) en Xamarin watchOS

Vea que el Kit proporciona un gesto Force Touch que desencadena un menú cuando se implementa en una pantalla de la aplicación de inspección.

![](menu-images/menu.png "Apple Watch que muestra un menú")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Responder al toque Force

Si un `Menu` se ha implementado para un controlador de interfaz, cuando un usuario realiza una Force Touch se abrirá el menú. Si no se ha implementado ningún menú, la pantalla se anima brevemente se produce una ninguna otra acción.

Force toques no están asociados con cualquier elemento en particular en la pantalla; solo menú puede asociarse a un controlador de interfaz y aparecerá con independencia de dónde se produce la acción de presionar Force Touch en la pantalla.

Entre uno y cuatro menú se pueden presentar opciones.


## <a name="adding-a-menu"></a>Adición de un menú

Un `Menu` deben agregarse a un `InterfaceController` en el guión gráfico en tiempo de diseño. Cuando se arrastra un control de menú a un controlador de interfaz no hay ninguna indicación visual en la vista previa del guión gráfico, pero la **menú** aparece en el **esquema del documento** panel:

![](menu-images/menu-action.png "Edición de un menú en tiempo de diseño")

Hasta cuatro menú se pueden agregar elementos al control de menú. Se pueden configurar en el **propiedades** pad. Se pueden establecer los siguientes atributos:

- Título, y
- Imagen personalizada, o
- Una imagen del sistema: Aceptar y agregar bloques, rechazo, Info, quizás, más, silenciar, pausa, reproducir y repetir, reanudar, recurso compartido, orden aleatorio, orador, la Papelera.

Crear un `Action` seleccionando el **eventos** sección de la **propiedades** panel y escriba el nombre para el método de acción. Se creará un método parcial en el código, que puede implementarse en la clase de controlador de interfaz, similar al siguiente:

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Imágenes personalizadas

Imágenes de elemento de menú similares a las imágenes de la pestaña de iOS, requieren un patrón opaco con un canal alfa que permite que el fondo muestre a través.

Debe agregar las imágenes utilizadas para el menú para el proyecto de aplicación de inspección (no proyecto de la extensión de aplicación watch) para obtener el mejor rendimiento.


## <a name="changing-the-menu-items"></a>Cambiar los elementos de menú

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Agregar en tiempo de ejecución

No puede hacer un `Menu` para agregarse a un controlador de interfaz en tiempo de ejecución, aunque la colección de `MenuItem`s *puede* se puede modificar mediante programación.
Use el `AddMenuItem` método tal como se muestra:

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

La API de Kit de inspección de Xamarin.iOS requiere actualmente una `selector` para el `AdMenuItem` método, que se debe declarar como similar al siguiente:

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Quitar en tiempo de ejecución

El `ClearAllMenuItems` se puede llamar el método para quitar todos *programación agregados* elementos de menú.

No se puede borrar los elementos de menú configurados en el guión gráfico.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento de menú de Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
