---
title: Trabajar con controladores de la barra de ficha en Xamarin de tvOS
description: Este documento describe cómo trabajar con controladores de la barra de pestaña en una aplicación de tvOS con Xamarin. Proporciona un alto nivel con respecto a la vista de barras de pestañas y se describen los elementos de la barra de ficha, integración de guiones gráficos y elementos de la barra de ficha.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: a0efc30fd9814e4da858c4e3e4e99990eccf102e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61164338"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Trabajar con controladores de la barra de ficha en Xamarin de tvOS

Para muchos tipos de aplicaciones de tvOS, navegación principal se presenta como una barra de pestañas que se ejecutan en la parte superior de la pantalla. El usuario pase el dedo de left y right a través de la lista de categorías posibles y el área de contenido por debajo de los cambios para reflejar la selección del usuario.

[![](tab-bars-images/tab01.png "Barra de pestañas de ejemplo")](tab-bars-images/tab01.png#lightbox)

La barra de pestañas es translúcida de forma predeterminada y siempre aparece en la parte superior de la pantalla. En el foco, una barra de pestañas cubrirá el 140 píxeles superiores de la pantalla, pero rápidamente deslice inmediatamente cuando el foco se desplaza hasta el área de contenido siguiente.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barras de pestañas de tvOS

El `UITabViewController` funciona de forma similar y tiene una finalidad similar en tvOS como lo hace en iOS, con las siguientes diferencias claves:

- A diferencia de la barra de pestañas en iOS, que aparece en la parte inferior de la pantalla, barras de pestañas en tvOS ocupan el 140 píxeles superiores de la pantalla y están translúcidas de forma predeterminada.
- Cuando el foco sale de la barra de pestañas para el área de contenido siguiente, la barra de pestañas se desliza rápidamente la parte superior de la pantalla y ocultarse. El usuario puede pulsar el botón de menú de una vez o deslícese hacia arriba en la [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para volver a mostrar la barra de pestañas.
- Dedo hacia abajo en el control remoto de Siri se moverá el foco al área de contenido por debajo de la barra de pestañas a la primera [elemento enfocable](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) en el contenido que se está mostrando. Nuevamente, Esto ocultará la barra de pestañas una vez que el foco se desplaza.
- Al hacer clic en para seleccionar una categoría que se muestra en la barra de pestañas, cambiará a la del categoría contenido y el foco se desactivará en el primer elemento enfocable en esa vista.
- Se debe corregir el número de categorías que se muestran en la barra de pestañas y todas las categorías deben ser accesibles en todo momento, nunca se debe deshabilitar una categoría determinada.
- Barras de pestañas no admite la personalización en tvOS. Además, no se muestran los **más** categoría (por ejemplo, iOS) si hay más categorías que puede caber en la barra de pestañas.

Apple tiene las siguientes sugerencias para trabajar con barras de pestañas:

- **Uso de barras de pestañas para organizar contenido lógicamente** -usar la barra de pestañas para organizar lógicamente el contenido que su aplicación tvOS funciona con. Por ejemplo, destacados, los gráficos de la parte superior, comprado y búsqueda.
- **Agregar notificaciones para informar a los usuarios de nuevo contenido** -opcionalmente, puede mostrar una notificación (una elipse con un número en blanco o el signo de exclamación rojo) para informar al usuario del nuevo contenido en una categoría.
- **Utilice con moderación distintivos** : no provocar un desorden en la barra de pestañas con notificaciones y solo mostrarlas que proporcionan información crítica para el usuario.
- **Limitar el número de categorías** : para reducir la complejidad y manejable, aplicación, no sobrecargar la barra de pestañas con categorías y asegúrese de que todas las categorías están visibles y no saturado. Títulos breves y sencillos funcionan mejor.
- **No deshabilite una categoría** -todas las pestañas (categorías) siempre debería estar visible y habilitado en todo momento. Si una ficha especificada no tiene contenido, ¿por qué dar una explicación al usuario. Por ejemplo, la ficha compras estará vacía si el usuario no ha realizado ninguna compra.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Elementos de la barra de pestañas

Cada categoría (pestaña) en la barra de pestañas está representada por un elemento de la barra de pestaña (`UITabBarItem`). Apple tiene las siguientes sugerencias para trabajar con elementos de la barra de pestaña:

- **Utilice las pestañas en función de texto** -mientras que el elemento de dicha barra es capaz de representar como un icono, Apple sugiere el uso de texto solo porque un título conciso es más fácil de interpretar que un icono.
- **Usar corta, nombres significativos o verbos** -un elemento de la barra de ficha debe retransmitir claramente el contenido que contiene y funciona mejor cuando es un nombre simple (por ejemplo, fotografías, música o películas) o los verbos (por ejemplo, buscar o reproducir).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barras de pestañas y los guiones gráficos

Es la manera más fácil trabajar con barras de pestañas en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
1. Inicie una nueva aplicación Xamarin.tvOS y seleccione **tvOS** > **aplicación** > **aplicación con pestañas**: 

    [![](tab-bars-images/tab02.png "Seleccione la aplicación con pestañas")](tab-bars-images/tab02.png#lightbox)
1. Siga todos los mensajes para crear una nueva solución Xamarin.tvOS.
1. En el **panel de solución**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Para cambiar la **icono** o **título** para una categoría determinada, seleccione el **elemento de la barra de ficha** para el **View Controller** en el  **Esquema de documento**:

    [![](tab-bars-images/tab03a.png "Elemento de la barra de pestañas para el controlador de vista en el esquema del documento")](tab-bars-images/tab03a.png#lightbox)
1. A continuación, establezca las propiedades necesarias el **ficha Widget** de la **Explorador de propiedades**: 

    [![](tab-bars-images/tab03.png "La pestaña de Widget")](tab-bars-images/tab03.png#lightbox)
1. Para agregar una nueva categoría (pestaña), coloque un **View Controller** a la superficie de diseño: 

    [![](tab-bars-images/tab04.png "Un controlador de vista")](tab-bars-images/tab04.png#lightbox)
1. Control y haga clic y arrastre desde el **ficha View Controller** al nuevo **View Controller**.
1. En la ventana emergente, seleccione **ver controladores** para agregar la nueva vista como una pestaña (categoría): 

    [![](tab-bars-images/tab05.png "Seleccione la ficha")](tab-bars-images/tab05.png#lightbox)
1. Cree el diseño de la interfaz de usuario para cada área de contenido Caterogies como es habitual, agregando elementos de interfaz de usuario en el Diseñador de iOS.
1. Exponer los eventos necesarios para trabajar con los controles de IU en C# código.
1. Los controles de interfaz de usuario que desea exponer en el nombre C# código.
1. Guarde los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Inicie una nueva aplicación Xamarin.tvOS y seleccione **tvOS** > **aplicación** > **aplicación con pestañas**: 

    [![](tab-bars-images/tab02vs.png "Seleccione la aplicación con pestañas")](tab-bars-images/tab02vs.png#lightbox)
1. Siga todos los mensajes para crear una nueva solución Xamarin.tvOS.
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo para su edición.
1. Para cambiar la **icono** o **título** para una categoría determinada, seleccione el **elemento de la barra de ficha** para el **View Controller** en el  **Esquema de documento**:

    [![](tab-bars-images/tab03avs.png "El controlador de vista en el esquema del documento")](tab-bars-images/tab03avs.png#lightbox)
1. A continuación, establezca las propiedades necesarias el **ficha Widget** de la **Explorador de propiedades**: 

    [![](tab-bars-images/tab03vs.png "La pestaña de Widget")](tab-bars-images/tab03vs.png#lightbox)
1. Para agregar una nueva categoría (pestaña), arrastre un **View Controller** desde el **cuadro de herramientas** y colóquela en la superficie de diseño: 

    [![](tab-bars-images/tab04vs.png "Un controlador de vista")](tab-bars-images/tab04vs.png#lightbox)
1. Control y haga clic y arrastre desde el **ficha View Controller** al nuevo **View Controller**.
1. En la ventana emergente, seleccione **ver controladores** para agregar la nueva vista como una pestaña (categoría): 

    [![](tab-bars-images/tab05vs.png "Seleccione la ficha")](tab-bars-images/tab05vs.png#lightbox)
1. Cree el diseño de la interfaz de usuario para cada área de contenido Caterogies como es habitual, agregando elementos de interfaz de usuario en el Diseñador de iOS.
1. Exponer los eventos necesarios para trabajar con los controles de IU en C# código.
1. Los controles de interfaz de usuario que desea exponer en el nombre C# código.
1. Guarde los cambios.
    
-----

> [!IMPORTANT]
> Aunque es posible asignar como eventos `TouchUpInside` a un elemento de interfaz de usuario (como un `UIButton`) en el Diseñador de iOS, nunca se llamará porque Apple TV no tiene una entrada táctil de pantalla o admitir eventos de toque. Siempre debe usar el `Primary Action ` evento al crear controladores de eventos para tvOS elementos de la interfaz de usuario.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Trabajar con barras de pestañas

Use la `Items` propiedad de la `UITabBar` para tener acceso a la colección de `UITabBarItems` contiene como una matriz indizada cero (0). El `SelectedItem` propiedad devolverá la pestaña seleccionada actualmente (categoría) como un `UITabBarItem`.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Trabajar con elementos de la barra de pestañas

Para mostrar un distintivo de una ficha especificada (una elipse con texto blanco rojo), use el código siguiente:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Lo que generaría los siguientes resultados cuando se ejecuta:

[![](tab-bars-images/tab06.png "Un elemento de la barra de ficha con el distintivo")](tab-bars-images/tab06.png#lightbox)

Use la `Title` propiedad de la `UITabBarItem` para cambiar el título y el `Image` propiedad para cambiar el icono.

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata de diseñar y trabajar con el controlador de la barra de pestañas dentro de una aplicación Xamarin.tvOS.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
