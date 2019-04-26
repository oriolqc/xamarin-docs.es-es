---
title: Menú emergente
description: Cómo agregar un menú emergente que se encuentra delimitado respecto a una vista determinada.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 1e74c8b7745936f6e9a8890fd26acafe2f2fb6d5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288661"
---
# <a name="popup-menu"></a>Menú emergente

El [menú emergente](https://developer.xamarin.com/api/type/Android.Widget.PopupMenu/) (también denominado una _menú contextual_) es un menú que se encuentra delimitado respecto a una vista determinada. En el ejemplo siguiente, una sola actividad contiene un botón. Cuando el usuario pulsa el botón, se muestra un menú emergente de tres elementos:

[![Ejemplo de una aplicación con un botón y un menú emergente de tres elementos](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>Creación de un menú emergente

El primer paso es crear un archivo de recursos de menú para el menú y colóquelo en **recursos o menú**. Por ejemplo, el siguiente código XML es el código para el menú de tres elementos que se muestra en la captura de pantalla anterior, **Resources/menu/popup_menu.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

A continuación, cree una instancia de `PopupMenu` y anclarlo a su vista. Cuando se crea una instancia de `PopupMenu`, pasar su constructor una referencia a la `Context` , así como la vista a la que se adjuntará el menú. Como resultado, el menú emergente está anclado a esta vista durante su construcción.

En el ejemplo siguiente, la `PopupMenu` se crea en el controlador de eventos click del botón (que se denomina `showPopupMenu`). Este botón también es la vista a la que el `PopupMenu` está anclada, tal como se muestra en el ejemplo de código siguiente:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Por último, debe ser el menú emergente *aumentada* con el recurso de menú que se creó anteriormente. En el ejemplo siguiente, la llamada en el menú [Inflate](https://developer.xamarin.com/api/member/Android.Views.LayoutInflater.Inflate/p/System.Int32/Android.Views.ViewGroup/) se agrega el método y su [mostrar](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Show%28%29/) método se llama para mostrar:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>Control de eventos de menú

Cuando el usuario selecciona un elemento de menú, el [MenuItemClick](https://developer.xamarin.com/api/event/Android.Widget.PopupMenu.MenuItemClick/) haga clic en, se generará el evento y se descartará el menú. Puntear en cualquier lugar fuera del menú se simplemente descarte. En cualquier caso, cuando se descarta el menú, su [DismissEvent](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Dismiss%28%29/) , se generará. El código siguiente agrega controladores de eventos tanto para el `MenuItemClick` y `DismissEvent` eventos:

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
