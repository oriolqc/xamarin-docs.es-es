---
title: Trabajar con la navegación en Xamarin watchOS
description: Este documento describe cómo trabajar con la navegación en una aplicación watchOS. Se trata de interfaces modales y navegación jerárquica, interfaces basadas en la página.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: c9bcfc388164060549ca7010d11671abfa8230ac
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790645"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Trabajar con la navegación en Xamarin watchOS

La opción de navegación más sencilla disponible en el reloj es un sencillo [emergente modal](#modal) que aparece encima de la escena actual.

Para aplicaciones de inspección de la escena se son dos paradigmas de navegación disponibles:

- [Navegación jerárquica](#Hierarchical_Navigation)
- [Interfaces basadas en la página](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfaces modales

Use la `PresentController` método para abrir un controlador de interfaz de forma modal. El controlador de interfaz ya debe estar definido en el **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Controladores de forma modal presentan use toda la pantalla (que abarcan la escena anterior). De forma predeterminada, el título se establece en **cancelar** y puntéelo descartará el controlador.

Para cerrar mediante programación el controlador presenta de forma modal, llame a `DismissController`.

```csharp
DismissController();
```

Pantallas modales pueden ser una sola escena o usar un diseño basado en la página.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navegación jerárquica

Plano como una pila que se puede navegar hacia atrás por, de forma similar a la forma en que se presenta `UINavigationController` funciona en iOS. Plano puede ser insertada en la pila de navegación y extrae (mediante programación o mediante la selección del usuario).

![](navigation-images/hierarchy-1.png "Plano se puede insertar en la pila de navegación") ![ ] (navigation-images/hierarchy-2.png "plano puede extrae de la pila de navegación")

Al igual que con iOS, un izquierda-borde-deslice el dedo navega hacia el controlador primario en una pila de navegación jerárquica.

Tanto el [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog) y [WatchTables](https://developer.xamarin.com/samples/WatchTables) ejemplos incluyen la navegación jerárquica.

### <a name="pushing-and-popping-in-code"></a>Insertar y retirar en código

Ver Kit no requiere un "controlador de navegación" cobros crearse como hace iOS - Inserte simplemente un controlador con el `PushController` método y una pila de navegación se crean automáticamente.

```csharp
PushController("secondPageController","some context info");
```

Pantalla de inspección incluirá un **Atrás** botón en la esquina superior izquierda, pero puede quitar mediante programación una escena de la pila de navegación utilizando `PopController`.

```csharp
PopController();
```

Igual que con iOS, también es posible volver a la raíz de la pila de navegación utilizando `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Usar Segues

Segues se pueden crear entre bastidores en el guión gráfico para definir la navegación jerárquica. Obtener el contexto de la escena de destino, las llamadas al sistema operativo `GetContextForSegue` para inicializar el nuevo controlador de interfaz.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```
<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>Interfaces basadas en la página

Deslice el dedo interfaces basadas en la página de izquierda a derecha, similar a la forma en que `UIPageViewController` funciona en iOS. Puntos de indicador se muestran a lo largo de la parte inferior de la pantalla para mostrar la página que se muestra actualmente.

![](navigation-images/paged-1.png "Primera página de ejemplo") ![ ] (navigation-images/paged-2.png "segunda página de ejemplo") ![ ] (navigation-images/paged-5.png "quinta página de ejemplo")


Para convertir una interfaz basada en la página de la interfaz de usuario principal de la aplicación de inspección, use `ReloadRootControllers` con una matriz de controladores de interfaz y contextos:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

También puede presentar un controlador en una página que no sea la raíz con `PresentController` de una de las demás escenas en una aplicación.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WatchTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchTables/)
