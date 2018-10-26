---
title: Trabajar con controladores de vista dividida en Xamarin de tvOS
description: Este documento describe cómo trabajar con tvOS dividir las vistas en una aplicación compilada con Xamarin. Proporciona una descripción general de los controladores de vista de división, cómo usarlos con guiones gráficos, obtener acceso a las vistas de maestro y detalles, mostrar y ocultar la vista maestra.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f1bd48378faa9ae6a4853083c93377268c38f01
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122197"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Trabajar con controladores de vista dividida en Xamarin de tvOS

Un controlador de vista dividida presenta y administra un maestro y el controlador de vista de detalle por en paralelo, en la pantalla al mismo tiempo. Controladores de vista de división se utiliza para presentar contenido persistente, puede recibir el foco en la vista principal (la sección más pequeña a la izquierda) y relacionados con los detalles en la vista de detalle (la sección mayor a la derecha).

[![](split-views-images/intro01.png "Vista de división de ejemplo")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Acerca de los controladores de vista dividida

Como se indicó anteriormente, un controlador de vista dividida administra un maestro y el controlador de vista de detalle que se presentan side-by-side, con el patrón que se va a la vista más pequeña a la izquierda, el detalle de mayor tamaño de la derecha. 

Además, la puede patrón controlador de vista se oculta o se muestra según sea necesario: 

[![](split-views-images/intro02.png "El controlador de vista maestra oculto")](split-views-images/intro02.png#lightbox)

A menudo se usan los controladores de vistas de división para presentar una lista de contenido pueden filtrar, con las categorías en la vista principal y los resultados filtrados en la vista de detalle. Esto normalmente se presenta como una vista de tabla a la izquierda y un [vista de colección](~/ios/tvos/user-interface/collection-views.md) a la derecha.

Al diseñar una interfaz de usuario que requiere un controlador de vista de división, Apple sugiere el uso principal y los controladores de vista de detalle que no cambian (solo los cambios de contenido, no la estructura). Si necesita controladores de vista de salida de intercambio, es mejor usar un controlador de navegación como la base de controlador de vista que se deba cambiar (principal o detalle).

Apple tiene las siguientes sugerencias para trabajar con controladores de vista de división:

- **Utilice el porcentaje de división correcto** : de forma predeterminada el controlador de vista dividida usa un tercio de la pantalla para el controlador de vista de maestro y dos tercios del controlador de vista de detalle. Si lo desea, puede usar una división 50/50. Elija el porcentaje correcto para que el contenido aparezca con equilibrio de la pantalla.
- **Conservar la selección principal** : mientras el contenido en el puede vista de detalle cambio es la respuesta a la selección de un usuario en la vista principal, debe corregirse el contenido de la vista principal. Además, debe mostrar claramente el elemento seleccionado actualmente en la vista maestra.
- **Utilice un único título unificada** -normalmente, querrá usar un título único, centrado en la vista de detalle, en lugar de un título en los detalles y la vista maestra.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Controladores de vista de división y guiones gráficos

Es la manera más fácil trabajar con controladores de vista dividida en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **panel de solución**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **controladores de vista dividida** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](split-views-images/activity01.png "Un controlador de vista dividida")](split-views-images/activity01.png#lightbox)
1. De forma predeterminada, el Diseñador de iOS se instalará un controlador de navegación y un controlador de vista en la vista maestra. Si esto no encaja con los requisitos de la aplicación, simplemente eliminarlas.
1. Si quita el valor predeterminado de vista principal, arrastre un nuevo controlador de vista hasta la superficie de diseño: 

    [![](split-views-images/activity02.png "Un controlador de vista")](split-views-images/activity02.png#lightbox)
1. Control y haga clic y arrastre desde el controlador de vista de división en el nuevo controlador de vista maestra. 
1. Seleccione **Master** desde el **menú emergente**: 

    [![](split-views-images/activity03.png "Seleccione el patrón en el menú emergente")](split-views-images/activity03.png#lightbox)
1. Diseñar el contenido de su maestro y vistas de detalle: 

    [![](split-views-images/activity04.png "Ejemplo de diseño")](split-views-images/activity04.png#lightbox)
1. Asignar **nombres** en el **ficha Widget** de la **panel de propiedades** para trabajar con los controles de IU en C# código.
1. Guarde los cambios y vuelva a Visual Studio para Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Arrastre un **controladores de vista dividida** desde el **cuadro de herramientas** y colóquela en la vista: 

    [![](split-views-images/activity01-vs.png "Un controlador de vista dividida")](split-views-images/activity01-vs.png#lightbox)
1. De forma predeterminada, el Diseñador de iOS agregará un controlador de navegación y el controlador de vista en la vista maestra. Si esto no encaja con los requisitos de la aplicación, simplemente eliminarlas.
1. Si quita el valor predeterminado de vista principal, arrastre un nuevo controlador de vista hasta la superficie de diseño: 

    [![](split-views-images/activity02-vs.png "Un controlador de vista")](split-views-images/activity02-vs.png#lightbox)
1. Control y haga clic y arrastre desde el controlador de vista de división en el nuevo controlador de vista maestra. 
1. Seleccione **Master** desde el **menú emergente**: 

    [![](split-views-images/activity03-vs.png "Seleccione el patrón en el menú emergente")](split-views-images/activity03-vs.png#lightbox)
1. Diseñar el contenido de su maestro y vistas de detalle: 

    [![](split-views-images/activity04.png "Diseño de contenido")](split-views-images/activity04.png#lightbox)
1. Asignar **nombres** en el **ficha Widget** de la **Explorador de propiedades** para trabajar con los controles de IU en C# código.
1. Guarde los cambios.
    
-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Trabajar con controladores de vista dividida

Como se indicó anteriormente, un controlador de vista de división se utiliza a menudo en situaciones donde se va a mostrar contenido filtrado para el usuario. Las principales categorías se muestran a la izquierda en la vista principal, y los resultados filtrados de la derecha en la vista de detalle según la selección del usuario.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Acceso a maestro y detalles

Si necesita tener acceso mediante programación el patrón y los controladores de vista de detalle, utilice el `ViewControllers ` propiedad del controlador de vista de división. Por ejemplo:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Se presenta como una matriz, donde el primer elemento (0) en el controlador de vista maestra y el segundo elemento (1) es el detalle.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Obtener acceso a los detalles del patrón

Puesto que normalmente se muestra información detallada en la vista de detalle según la selección del usuario en el servidor maestro, necesitará una manera de obtener acceso a los detalles del servidor maestro.

La manera más fácil de hacerlo es exponer una propiedad en la clase de controlador de vista maestra, por ejemplo:

```csharp
public DetailViewController DetailController { get; set;}
```

En el controlador de vista dividida, invalide el `ViewDidLoad` método equivalentes las dos vistas y juntos. Por ejemplo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

Puede exponer propiedades y métodos en el controlador de vista de detalle que el maestro puede utilizar para presentar datos nuevos, según sea necesario.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Mostrar y ocultar Master

Si lo desea, puede mostrar y ocultar el controlador de vista maestra con el `PreferredDisplayMode` propiedad del controlador de vista de división. Por ejemplo:

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

El `UISplitViewControllerDisplayMode` enumeración define cómo se presentará el controlador de vista maestra como uno de los siguientes:

- **Automática** -tvOS controlará la presentación de las vistas de detalle y maestra.
- **PrimaryHidden** -Esto oculta el controlador de vista maestra.
- **AllVisible** -muestra el patrón y lo controladores de vista de detalle paralelo. Se trata de la normal, presentación predeterminada.
- **PrimaryOverlay** -extiende en el controlador de vistas de detalle y está cubierto por el patrón.

Para obtener el estado de presentación actual, use el `DisplayMode` propiedad del controlador de vista de división.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con controladores de vista de división dentro de una aplicación Xamarin.tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
