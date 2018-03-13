---
title: "Menú emergente"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/18/2017
ms.openlocfilehash: f976d798ae1b1279fc8f82d3cf1d738bb2c93911
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="popup-menu"></a>Menú emergente

La `PopupMenu` clase agrega compatibilidad para mostrar los menús emergentes que están conectados a una vista determinada. La ilustración siguiente muestra un menú emergente que se presentan en un botón, el resaltado solo cuando se selecciona el segundo elemento:

 [![Ejemplo de un PopopMenu con tres tres elementos](popup-menu-images/20-popupmenu.png)](popup-menu-images/20-popupmenu.png#lightbox)

Android 4 agrega un par de características nuevas para `PopupMenu` que facilitan un poco más fácil trabajar con, a saber:

-   **Inflate** &ndash; aumentar el método ahora está disponible directamente en la clase del menú emergente.
-   **DismissEvent** &ndash; menú emergente de la clase tiene ahora un DismissEvent.

¡Eche un vistazo a estas mejoras. En este ejemplo, tenemos una sola actividad que contiene un botón. Cuando el usuario hace clic en el botón, se muestra un menú emergente tal y como se muestra a continuación:

 [![Ejemplo de aplicación que se ejecuta en un emulador con los botones y menús emergentes 3-item](popup-menu-images/06-popupmenu.png)](popup-menu-images/06-popupmenu.png#lightbox)


## <a name="creating-a-popup-menu"></a>Crear un menú emergente

Cuando se crea una instancia de la `PopupMenu`, es necesario pasar su constructor una referencia a la `Context`, así como la vista al que está asociado el menú. En este caso, creamos la `PopupMenu` en el controlador de eventos click para el botón, que se denomina `showPopupMenu`.
Este botón también es la vista a la que se asociará el `PopupMenu`, como se muestra en el código siguiente:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
}
```

En Android 3, el código para aumentar el menú de un recurso XML necesario obtener una referencia a un `MenuInflator`y, a continuación, llame a su `Inflate` método con el identificador de recurso de XML que contiene el menú y la instancia de menú para aumentar en. Este enfoque funciona aún en Android 4 y posterior a medida que el código siguiente muestra:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.MenuInflater.Inflate (Resource.Menu.popup_menu, menu.Menu);
};
```

A partir de Android 4 sin embargo, ahora puede llamar `Inflate` directamente en la instancia de la `PopupMenu`. Esto hace que el código más conciso tal y como se muestra aquí:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

En el código anterior, después de que infla el menú simplemente llamamos `menu.Show` para mostrarla en la pantalla.


## <a name="handling-menu-events"></a>Control de eventos de menú

Cuando el usuario selecciona un elemento de menú, el `MenuItemClick` se generará el evento y se omitirá el menú. Puntear en cualquier lugar fuera del menú se simplemente descartarla. En cualquier caso, a partir de Android 4, cuando se cierra el menú, su `DismissEvent` se generará. El código siguiente agrega controladores de eventos tanto para el `MenuItemClick` y `DismissEvent` eventos:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
            menu.Show ();
};
```



## <a name="related-links"></a>Vínculos relacionados

- [PopupMenuDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
- [Introducción a helado Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
