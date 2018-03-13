---
title: "Funcionan con el controlador de la barra de pestañas"
description: "Este artículo tratan diseñar y trabajar con el controlador de la barra de ficha dentro de una aplicación Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fc9765b3f6a77f47fdce32dbc3805dd7bd70a08a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-tab-bar-controller"></a>Funcionan con el controlador de la barra de pestañas

_Este artículo tratan diseñar y trabajar con el controlador de la barra de ficha dentro de una aplicación Xamarin.tvOS._

Para muchos tipos de aplicaciones de tvOS, navegación principal se presenta como una barra de pestañas dispuestos a lo largo de la parte superior de la pantalla. El usuario lectores de crédito izquierdo y derecho a través de la lista de categorías posibles y el área de contenido por debajo de los cambios para reflejar la selección del usuario.

[![](tab-bars-images/tab01.png "Barra de pestañas de ejemplo")](tab-bars-images/tab01.png#lightbox)

La barra de pestañas es translúcida de forma predeterminada y siempre aparece en la parte superior de la pantalla. En el foco, una barra de pestañas cubrirá el 140 píxeles superiores de la pantalla pero rápidamente deslice inmediatamente cuando el foco se desplaza hasta el área de contenido siguiente.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Ficha barras en tvOS

El `UITabViewController` funciona de forma similar y sirve para un propósito similar en tvOS como se hace en iOS, con las siguientes diferencias claves:

- A diferencia de la barra de pestañas en iOS que aparece en la parte inferior de la pantalla, barras de ficha en tvOS ocupan el 140 píxeles superiores de la pantalla y se translúcido de forma predeterminada.
- Cuando la barra de pestañas para el área de contenido siguiente pierde el foco, la barra de pestañas se deslice rápidamente la parte superior de la pantalla y se oculta. El usuario puede pulsar el botón de menú de una vez o deslícese hacia arriba en la [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para volver a mostrar la barra de pestañas.
- Deslizar rápidamente hacia abajo en el control remoto Siri moverá foco al área de contenido por debajo de la barra de pestañas a la primera [elemento puede recibir el foco](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) en el contenido que se está mostrando. Una vez más, Esto ocultará la barra de pestañas una vez que el foco se desplaza.
- Haga clic para seleccionar una categoría que se muestra en la barra de pestañas, cambiará al contenido y el foco de la categoría se cambiará al primer elemento puede recibir el foco en esa vista.
- El número de categorías que se muestran en la barra de pestañas deberá corregirse y todas las categorías deben ser accesibles en todo momento, nunca se debe deshabilitar una categoría determinada.
- Barras de ficha no admite la personalización en tvOS. Además, no muestran la **más** categoría (por ejemplo, iOS) si hay más categorías que caben en la barra de pestañas.

Apple tiene las siguientes sugerencias para trabajar con las barras de la pestaña:

- **Use las barras de pestaña para organizar contenido lógicamente** -utilice la barra de pestañas para organizar de forma lógica el contenido que la aplicación tvOS funciona con. Por ejemplo, destacado, gráficos de la parte superior, comprado y búsqueda.
- **Agregar notificaciones para informar a los usuarios del nuevo contenido** -opcionalmente, puede mostrar un distintivo (una elipse con un número de blanco o un signo de exclamación rojo) para informar al usuario de nuevo contenido en una categoría.
- **Utilice con moderación distintivos** : no desordenar la barra de pestañas con notificaciones y mostrarlos sólo donde proporcionan información crítica para el usuario.
- **Limitar el número de categorías** : para reducir la complejidad y mantener tu aplicación administrable, no sobrecargar la barra de pestañas con categorías y asegúrese de que todas las categorías son visibles y no llena. Títulos simples, short funcionan mejor.
- **No deshabilite una categoría** -todas las fichas (categorías) siempre debería estar visible y habilitado en todo momento. Si una ficha especificada no tiene contenido, proporcionan una explicación para el usuario, ¿por qué. Por ejemplo, la pestaña Compras estará vacía si el usuario no ha realizado ninguna compra.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Elementos de la barra de pestañas

Cada categoría (ficha) en la barra de pestañas está representada por un elemento de la barra de pestaña (`UITabBarItem`). Apple tiene las siguientes sugerencias para trabajar con elementos de la barra de ficha:

- **Use las pestañas en función de texto** -mientras que el elemento de barra de ficha es capaz de representarse como un icono, Apple sugiere utilizando texto solo porque un título conciso es más fácil de interpretar que la de un icono.
- **Usar corta, nombres significativos o verbos** -un elemento de la barra de ficha debe retransmitir claramente el contenido que lo contiene y funciona mejor cuando se trata de un nombre simple (por ejemplo, fotos, música o películas) o verbos (por ejemplo, buscar o Play).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barras de ficha y los guiones gráficos

Es la manera más fácil trabajar con barras de ficha en una aplicación Xamarin.tvOS agregarlos a la interfaz de usuario de la aplicación mediante el Diseñador de iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. Inicie una nueva aplicación de Xamarin.tvOS y seleccione **tvOS** > **aplicación** > **por pestañas aplicación**: 

    [![](tab-bars-images/tab02.png "Seleccione la aplicación con pestañas")](tab-bars-images/tab02.png#lightbox)
1. Siga todas las instrucciones para crear una nueva solución de Xamarin.tvOS.
1. En el **solución Pad**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Para cambiar la **icono** o **título** para una categoría determinada, seleccione la **elemento de la barra de ficha** para el **View Controller** en el  **Esquema de documento**:

    [![](tab-bars-images/tab03a.png "Elemento de la barra de pestañas para el controlador de vista en el esquema del documento")](tab-bars-images/tab03a.png#lightbox)
1. A continuación, establezca las propiedades necesarias el **ficha Widget** de la **el Explorador de propiedades**: 

    [![](tab-bars-images/tab03.png "La pestaña de Widget")](tab-bars-images/tab03.png#lightbox)
1. Para agregar una nueva categoría (ficha), coloque un **View Controller** a la superficie de diseño: 

    [![](tab-bars-images/tab04.png "Un controlador de vista")](tab-bars-images/tab04.png#lightbox)
1. Control y haga clic y arrastre desde el **ficha View Controller** al nuevo **View Controller**.
1. En la ventana emergente, seleccione **ver controladores** para agregar la nueva vista como una pestaña (categoría): 

    [![](tab-bars-images/tab05.png "Seleccione la pestaña")](tab-bars-images/tab05.png#lightbox)
1. Cree el diseño de la interfaz de usuario para cada área de contenido de Caterogies como es habitual, mediante la adición de elementos de interfaz de usuario en el Diseñador de iOS.
1. Exponer los eventos necesarios para trabajar con los controles de interfaz de usuario en código C#.
1. Nombre de los controles de interfaz de usuario que desea exponer en código C#.
1. Guarde los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Inicie una nueva aplicación de Xamarin.tvOS y seleccione **tvOS** > **aplicación** > **por pestañas aplicación**: 

    [![](tab-bars-images/tab02vs.png "Seleccione la aplicación con pestañas")](tab-bars-images/tab02vs.png#lightbox)
1. Siga todas las instrucciones para crear una nueva solución de Xamarin.tvOS.
1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` de archivo y abrirlo y editarlo.
1. Para cambiar la **icono** o **título** para una categoría determinada, seleccione la **elemento de la barra de ficha** para el **View Controller** en el  **Esquema de documento**:

    [![](tab-bars-images/tab03avs.png "El controlador de vista en el esquema del documento")](tab-bars-images/tab03avs.png#lightbox)
1. A continuación, establezca las propiedades necesarias el **ficha Widget** de la **el Explorador de propiedades**: 

    [![](tab-bars-images/tab03vs.png "La pestaña de Widget")](tab-bars-images/tab03vs.png#lightbox)
1. Para agregar una nueva categoría (ficha), arrastre un **View Controller** desde el **cuadro de herramientas** y se coloca en la superficie de diseño: 

    [![](tab-bars-images/tab04vs.png "Un controlador de vista")](tab-bars-images/tab04vs.png#lightbox)
1. Control y haga clic y arrastre desde el **ficha View Controller** al nuevo **View Controller**.
1. En la ventana emergente, seleccione **ver controladores** para agregar la nueva vista como una pestaña (categoría): 

    [![](tab-bars-images/tab05vs.png "Seleccione la pestaña")](tab-bars-images/tab05vs.png#lightbox)
1. Cree el diseño de la interfaz de usuario para cada área de contenido de Caterogies como es habitual, mediante la adición de elementos de interfaz de usuario en el Diseñador de iOS.
1. Exponer los eventos necesarios para trabajar con los controles de interfaz de usuario en código C#.
1. Nombre de los controles de interfaz de usuario que desea exponer en código C#.
1. Guarde los cambios.
    
-----

> [!IMPORTANT]
> **Nota:** aunque es posible asignar eventos como `TouchUpInside` a un elemento de interfaz de usuario (como un `UIButton`) en el Diseñador de iOS, nunca se llamará Apple TV no tiene un toque la pantalla o admitir eventos de toque. Debe utilizar siempre el `Primary Action ` eventos al crear controladores de eventos para tvOS elementos de la interfaz de usuario.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Trabajar con barras de pestaña

Use la `Items` propiedad de la `UITabBar` para tener acceso a la colección de `UITabBarItems` contiene como una matriz indizada cero (0). El `SelectedItem` propiedad devolverá la pestaña seleccionada actualmente (categoría) como un `UITabBarItem`.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Trabajar con elementos de la barra de pestañas

Para mostrar un distintivo en una ficha determinada (una elipse roja con texto en blanco), utilice el código siguiente:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Que generan los siguientes resultados cuando se ejecuta:

[![](tab-bars-images/tab06.png "Un elemento de barra de ficha con el distintivo")](tab-bars-images/tab06.png#lightbox)

Use la `Title` propiedad de la `UITabBarItem` para cambiar el título y el `Image` propiedad para cambiar el icono.

<a name="Summary" />

## <a name="summary"></a>Resumen

Diseñar y trabajar con el controlador de la barra de ficha dentro de una aplicación Xamarin.tvOS se ha descrito en este artículo.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
