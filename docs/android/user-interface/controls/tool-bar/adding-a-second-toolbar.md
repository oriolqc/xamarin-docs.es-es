---
title: Adición de una segunda barra de herramientas
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: fc95c05c1945464cd9cac8565d8a11ff1b4c7e1d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61175171"
---
# <a name="adding-a-second-toolbar"></a>Adición de una segunda barra de herramientas


## <a name="overview"></a>Información general 

El `Toolbar` puede realizar algo más que reemplazar la barra de acciones &ndash; pueden utilizarse varias veces dentro de una actividad, se puede personalizar para su ubicación en cualquier lugar en la pantalla y se puede configurar para abarcar sólo un ancho parcial de la pantalla. Los ejemplos siguientes muestran cómo crear un segundo `Toolbar` y colóquelo en la parte inferior de la pantalla. Esto `Toolbar` implementa **copia**, **cortar**, y **pegar** elementos de menú. 


## <a name="define-the-second-toolbar"></a>Definir la segunda barra de herramientas 

Editar el archivo de diseño **Main.axml** y reemplace su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

Este código XML agrega un segundo `Toolbar` hasta la parte inferior de la pantalla con un valor vacío `ImageView` rellenando el medio de la pantalla. El alto de este `Toolbar` se establece el alto de una barra de acciones: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

El color de fondo de este `Toolbar` está establecido en un color de énfasis que se define a continuación:

```xml
android:background="?android:attr/colorAccent
```

Tenga en cuenta que este `Toolbar` se basa en un tema diferente (**ThemeOverlay.Material.Dark.ActionBar**) del que utilizan el `Toolbar` creado en [reemplazando la barra de acciones](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;no está enlazado a la decoración de la ventana de la actividad o en el tema utilizado en la primera `Toolbar`.

Editar **Resources/values/styles.xml** y agregue el siguiente color de énfasis a la definición de estilo: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Esto proporciona la barra de herramientas de la parte inferior de un color ámbar oscuro. Compilar y ejecutar la aplicación muestran una segunda barra de herramientas en blanco en la parte inferior de la pantalla: 

[![Captura de pantalla de la aplicación con la barra de herramientas del segundo amarillo en la parte inferior de la pantalla](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Agregar elementos de menú de edición 

Esta sección explica cómo agregar elementos de menú de edición a la parte inferior `Toolbar`. 

Para agregar elementos de menú a una base de datos secundaria `Toolbar`: 

1.  Agregar iconos de menú a la `mipmap-` carpetas del proyecto de aplicación (si es necesario).

2.  Definir el contenido de los elementos de menú mediante la adición de un archivo de recursos de menú adicionales a **recursos o menú**. 

3.  En la actividad `OnCreate` método, buscar el `Toolbar` (mediante una llamada a `FindViewById`) y aumentar la `Toolbar`de menús.

4.  Implemente un controlador de clic en `OnCreate` para los nuevos elementos de menú. 

Las secciones siguientes muestran este proceso en detalle: **Cortar**, **copia**, y **pegar** elementos de menú se agregan a la parte inferior `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Definir el recurso de menú Edición

En el **recursos o menú** subdirectorio, cree un archivo XML nuevo llamado **edit_menus.xml** y reemplace el contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Este XML crea el **cortar**, **copia**, y **pegar** elementos de menú (mediante iconos que se agregaron a la `mipmap-` carpetas en [reemplazando la barra de acciones ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Infla los menús

Al final de la `OnCreate` método **MainActivity.cs**, agregue las siguientes líneas de código: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Este código localiza el `edit_toolbar` vista definida en **Main.axml**, configura su título **edición**y aumenta sus elementos de menú (definido en **edit_menus.xml**). Define un menú haga clic en el controlador que se muestra una notificación del sistema para indicar que se ha punteado con el icono de edición. 

Compile y ejecute la aplicación. Cuando se ejecuta la aplicación, el texto y los iconos agregados anteriormente aparecerán como se muestra aquí: 

[![Diagrama de la parte inferior de la barra de herramientas con iconos de cortar, copiar y pegar](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Pulsar el **cortar** icono de menú hace que el aviso siguiente que se mostrará: 

[![Captura de pantalla de aviso que indica que se ha punteado con el icono de menú Cortar](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Al puntear en los elementos de menú en cualquier barra de herramientas muestra las notificaciones del sistema resultantes: 

[![Capturas de pantalla notificaciones de sistema para guardar, copiar y pegar elementos de menú que se pulsa](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>El botón Subir 

Dependen de la mayoría de las aplicaciones Android el **Atrás** botón de navegación de la aplicación; al presionar el **volver** botón lleva al usuario a la pantalla anterior.
Sin embargo, también puede proporcionar un **seguridad** botón que facilita a los usuarios naveguen "hasta" en la pantalla principal de la aplicación. Cuando el usuario selecciona el **seguridad** button, el usuario se mueve a un nivel superior en la jerarquía de la aplicación &ndash; es decir, la aplicación extrae el usuario vuelve varias actividades en la pila de retroceso en lugar de que se extrae de nuevo a ha visitado Actividad. 

Para habilitar el **seguridad** botón en una segunda actividad que usa un `Toolbar` como su barra de acciones, llame a la `SetDisplayHomeAsUpEnabled` y `SetHomeButtonEnabled` métodos en la segunda actividad `OnCreate` método:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

El [admiten la barra de herramientas v7](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) código de ejemplo muestra el **seguridad** botón en acción. Este ejemplo (que usa la biblioteca de AppCompat se describe a continuación) implementa una segunda actividad que usa la barra de herramientas **seguridad** botón de navegación jerárquica a la actividad anterior. En este ejemplo, el `DetailActivity` habilita el botón de inicio la **seguridad** botón haciendo lo siguiente `SupportActionBar` llamadas al método: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Cuando el usuario navega de `MainActivity` a `DetailActivity`, `DetailActivity` muestra un **seguridad** botón (flecha izquierda señalador) como se muestra en la captura de pantalla:

[![Captura de pantalla ejemplo de un botón izquierdo arriba en la barra de herramientas](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Pulsar esto **seguridad** botón hace que la aplicación volver a `MainActivity`. En una aplicación más compleja con varios niveles de jerarquía, pulsa en este botón devolvía el usuario al siguiente nivel más alto en la aplicación en lugar de a la pantalla anterior. 



## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas del círculo (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de herramientas de AppCompat (ejemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
