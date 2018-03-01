---
title: "Trabajar con controladores de la vista de división"
description: "Este artículo tratan diseñar y trabajar con controladores de la vista de división dentro de una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8787913c04b11a84828cd98960407f0cc27aa391
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-split-view-controllers"></a>Trabajar con controladores de la vista de división

_Este artículo tratan diseñar y trabajar con controladores de la vista de división dentro de una aplicación Xamarin.tvOS._


Un controlador de vista de división presenta y administra un maestro y detalles View Controller-paralelo, en la pantalla al mismo tiempo. Se utiliza para presentar contenido persistente, puede recibir el foco en la vista maestra (sección más pequeña a la izquierda) y relacionados con controladores de la vista de división detalles en la vista de detalle (es decir, la sección mayor a la derecha).

[ ![](split-views-images/intro01.png "Vista de división de ejemplo")](split-views-images/intro01.png)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Acerca de los controladores de la vista de división

Como se mencionó anteriormente, un controlador de vista de división administra un patrón y un controlador de vista de detalle que se presentan en paralelo, con el patrón que se va a la vista más pequeña a la izquierda, el detalle el mayor de la derecha. 

Además, se oculta o se muestra el puede Master View Controller según sea necesario: 

[ ![](split-views-images/intro02.png "El controlador de vista maestra oculto")](split-views-images/intro02.png)

Controladores de vistas de división a menudo se utilizan para presentar una lista de contenido se puede filtrar, con los resultados filtrados en la vista de detalle y de las categorías en la vista maestra. Esto normalmente se presenta como una vista de tabla de la izquierda y un [vista de colección](~/ios/tvos/user-interface/collection-views.md) a la derecha.

Al diseñar una interfaz de usuario que requiere un controlador de vista de división, Apple sugiere utilizando Master y controladores de la vista de detalle que no cambian (sólo los cambios de contenido, no la estructura). Si necesita ver controladores de espera de intercambio, es mejor usar un controlador de navegación como la base de controlador de vista que se deba cambiar (Master o detalles).

Apple tiene las siguientes sugerencias para trabajar con controladores de la vista de división:

- **Utilice el porcentaje de división correcto** : de forma predeterminada el controlador de vista de división utiliza una tercera parte de la pantalla para el controlador de vista maestra y dos tercios para el controlador de vista de detalle. Si lo desea, puede usar una división 50/50. Elija el porcentaje correcto para que su contenido aparezca equilibrada en pantalla.
- **Conservar la selección de Main** : mientras el contenido en el puede vista de detalle cambio es la respuesta a la selección de un usuario en la vista maestra, deberá corregirse el contenido de la vista maestra. Además, deben mostrar claramente el elemento seleccionado actualmente en la vista maestra.
- **Usar una única unificada título** -normalmente, deseará usar un título único, centrado en la vista de detalles, en lugar de un título en los detalles y la vista maestra.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Ver controladores de división y los guiones gráficos

Es la manera más fácil trabajar con controladores de la vista de división en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **solución Pad**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **controladores de la vista de división** desde el **cuadro de herramientas** y colóquela en la vista: 

    [ ![](split-views-images/activity01.png "Un controlador de vista de división")](split-views-images/activity01.png)
1. De forma predeterminada, el Diseñador de iOS instalará un controlador de navegación y un controlador de vista en la vista maestra. Si esto no encaja con los requisitos de la aplicación, simplemente eliminarlas.
1. Si quita el valor predeterminado de la vista maestra, arrastre un nuevo controlador de vista a la superficie de diseño: 

    [ ![](split-views-images/activity02.png "Un controlador de vista")](split-views-images/activity02.png)
1. Control y haga clic y arrastre desde el controlador de vista de división en el nuevo controlador de vista maestra. 
1. Seleccione **Master** desde el **menú emergente**: 

    [ ![](split-views-images/activity03.png "Seleccione patrón en el menú emergente")](split-views-images/activity03.png)
1. Diseñe el contenido de su maestro y vistas de detalles: 

    [ ![](split-views-images/activity04.png "Ejemplo de diseño")](split-views-images/activity04.png)
1. Asignar **nombres** en el **ficha Widget** de la **panel de propiedades** para trabajar con los controles de interfaz de usuario en código C#.
1. Guardar los cambios y volver a Visual Studio para Mac.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Arrastre un **controladores de la vista de división** desde el **cuadro de herramientas** y colóquela en la vista: 

    [ ![](split-views-images/activity01-vs.png "Un controlador de vista de división")](split-views-images/activity01-vs.png)
1. De forma predeterminada, el Diseñador de iOS se agregará un controlador de navegación y el controlador de vista en la vista maestra. Si esto no encaja con los requisitos de la aplicación, simplemente eliminarlas.
1. Si quita el valor predeterminado de la vista maestra, arrastre un nuevo controlador de vista a la superficie de diseño: 

    [ ![](split-views-images/activity02-vs.png "Un controlador de vista")](split-views-images/activity02-vs.png)
1. Control y haga clic y arrastre desde el controlador de vista de división en el nuevo controlador de vista maestra. 
1. Seleccione **Master** desde el **menú emergente**: 

    [ ![](split-views-images/activity03-vs.png "Seleccione patrón en el menú emergente")](split-views-images/activity03-vs.png)
1. Diseñe el contenido de su maestro y vistas de detalles: 

    [ ![](split-views-images/activity04.png "Diseño de contenido")](split-views-images/activity04.png)
1. Asignar **nombres** en el **ficha Widget** de la **el Explorador de propiedades** para trabajar con los controles de interfaz de usuario en código C#.
1. Guarde los cambios.
    
-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Trabajar con controladores de la vista de división

Como se mencionó anteriormente, un controlador de vista de la división se utiliza a menudo en situaciones donde se muestran contenido filtrado para el usuario. Se muestran las categorías principales de la izquierda en la vista patrón, y los resultados filtrados a la derecha en la vista de detalle según la selección del usuario.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Obtener acceso a maestro y detalles

Si necesita tener acceso mediante programación maestra y controladores de la vista de detalle, use la `ViewControllers ` propiedad del controlador de vista de división. Por ejemplo:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Se presenta como una matriz, donde el primer elemento (0) en el controlador de vista maestra y el segundo elemento (1) es el detalle.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Obtener acceso a los detalles del maestro

Puesto que normalmente se muestra información detallada en la vista de detalle en función de la selección del usuario de la página maestra, necesitará una manera de tener acceso a los detalles del servidor maestro.

La manera más fácil de hacerlo es exponer una propiedad en la clase de controlador de vista maestra, por ejemplo:

```csharp
public DetailViewController DetailController { get; set;}
```

En el controlador de vista de división, invalida el `ViewDidLoad` método y Lazo conjuntamente las dos vistas. Por ejemplo:

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

Puede exponer propiedades y métodos en el controlador de vista de detalle que el maestro se puede utilizar para crear nuevos datos según sea necesario.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Mostrar u ocultar Master

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
- **AllVisible** -muestra el patrón y lo controladores de la vista de detalle en paralelo. Se trata de la presentación predeterminada de su estado normal.
- **PrimaryOverlay** -extiende en el controlador de vista de detalle y está cubierto por el patrón.

Para obtener el estado de presentación actual, use el `DisplayMode` propiedad del controlador de vista de división.

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con controladores de la vista de división dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
