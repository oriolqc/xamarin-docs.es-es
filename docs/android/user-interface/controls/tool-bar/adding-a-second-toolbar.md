---
title: Agregar una segunda barra de herramientas
ms.topic: article
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: b471742ae9fb365d75e8dd3ca0f93f5e55208f19
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="adding-a-second-toolbar"></a>Agregar una segunda barra de herramientas

<a name="overview" />

## <a name="overview"></a>Información general 

El `Toolbar` puede llevar a cabo más que reemplazar la barra de acción &ndash; se puede utilizar varias veces dentro de una actividad, puede ser personalizado para la ubicación en cualquier parte de la pantalla y se puede configurar para abarcar sólo un ancho parcial de la pantalla. Los ejemplos siguientes muestran cómo crear un segundo `Toolbar` y lo coloca en la parte inferior de la pantalla. Esto `Toolbar` implementa **copia**, **cortar**, y **pegar** elementos de menú. 

<a name="define_second" />

## <a name="define-the-second-toolbar"></a>Definir la segunda barra de herramientas 

Editar el archivo de diseño **Main.axml** y sustituirlos por su contenido con el siguiente código XML:

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

Este código XML agrega un segundo `Toolbar` a la parte inferior de la pantalla con vacío `ImageView` rellenar la mitad de la pantalla. El alto de esta `Toolbar` se establece en el alto de una barra de acciones: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

El color de fondo de este `Toolbar` se establece en un color de énfasis que se definen a continuación:

```xml
android:background="?android:attr/colorAccent
```

Observe que este `Toolbar` se basa en un tema diferente (**ThemeOverlay.Material.Dark.ActionBar**) que usa el `Toolbar` creado en [reemplazar la barra de acciones](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;no está enlazada a la decoración de ventana de la actividad o en el tema utilizado en la primera `Toolbar`.

Editar **Resources/values/styles.xml** y agregue el siguiente color de énfasis en la definición de estilo: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Esto proporciona la barra de herramientas de la parte inferior de un color ámbar oscuro. Compilar y ejecutar la aplicación muestran una segunda barra de herramientas en blanco en la parte inferior de la pantalla: 

[![Captura de pantalla de aplicación con amarillo segunda barra de herramientas en la parte inferior de la pantalla](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png)


<a name="second_menus" />
 
## <a name="add-edit-menu-items"></a>Agregar elementos de menú de edición 

Esta sección explica cómo agregar elementos de menú de edición a la parte inferior `Toolbar`. 

Para agregar elementos de menú a una base de datos secundaria `Toolbar`: 

1.  Agregar iconos de menú a la `mipmap-` carpetas de proyecto de la aplicación (si es necesario).

2.  Definir el contenido de los elementos de menú mediante la adición de un archivo de recursos de menú adicionales a **recursos/menú**. 

3.  En la actividad `OnCreate` método, buscar el `Toolbar` (mediante una llamada a `FindViewById`) y aumentar la `Toolbar`de menús.

4.  Implemente un controlador de clic en `OnCreate` para los nuevos elementos de menú. 

Las secciones siguientes muestran este proceso en detalle: **cortar**, **copia**, y **pegar** elementos de menú se agregan a la parte inferior `Toolbar`. 


<a name="second_resource" />

### <a name="define-the-edit-menu-resource"></a>Definir el recurso de menú de edición

En el **recursos/menú** subdirectorio, cree un nuevo archivo XML denominado **edit_menus.xml** y reemplace el contenido con el siguiente código XML:

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

Este XML crea el **cortar**, **copia**, y **pegar** elementos de menú (utilizando los iconos que se agregaron a la `mipmap-` carpetas de [reemplazar la barra de acciones ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).


<a name="inflate_menus" />

### <a name="inflate-the-menus"></a>Aumentar los menús

Al final de la `OnCreate` método **MainActivity.cs**, agregue las siguientes líneas de código: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Este código localiza el `edit_toolbar` vista definida en **Main.axml**, establece su título en **edición**y lo aumenta sus elementos de menú (definido en **edit_menus.xml**). Define un menú haga clic en el controlador que se muestra una notificación del sistema para indicar el icono de edición que se puntea. 

Compile y ejecute la aplicación. Cuando se ejecuta la aplicación, los iconos agregadas anteriormente y el texto aparecerá como se muestra aquí: 

[![Diagrama de la parte inferior de la barra de herramientas con iconos de cortar, copiar y pegar](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png)

Al puntear en el **cortar** icono del menú hace que el sistema siguiente que se mostrará: 

[![Captura de pantalla de notificación que indica se puntea en el icono de menú Cortar](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png)

Pulse en elementos de menú en cualquier barra de herramientas muestra las notificaciones del sistema resultantes: 

[![Capturas de pantalla de notificaciones del sistema para guardar, copiar y pegar elementos de menú que se puntea](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png)


<a name="up_button" />

## <a name="the-up-button"></a>El botón Subir 

Mayoría de las aplicaciones Android se basan en el **volver** botón de navegación de la aplicación; al presionar la **volver** botón lleva al usuario a la pantalla anterior.
Sin embargo, también puede proporcionar un **seguridad** botón que facilita a los usuarios se desplacen "hasta" en la pantalla principal de la aplicación. Cuando el usuario selecciona el **una** botón, el usuario se desplaza hacia arriba a un nivel superior en la jerarquía de la aplicación &ndash; es decir, la aplicación extrae el usuario vuelve a varias actividades en la pila de retroceso en lugar de extraer back previamente visitadas Actividad. 

Para habilitar la **una** botón en una segunda actividad que usa un `Toolbar` como su barra de acciones, llame a la `SetDisplayHomeAsUpEnabled` y `SetHomeButtonEnabled` métodos en la segunda actividad `OnCreate` método:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

El [admite la barra de herramientas de v7](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) ejemplo de código se muestra la **seguridad** botón de acción. Este ejemplo (que utiliza la biblioteca de AppCompat se describe a continuación) implementa una segunda actividad que usa la barra de herramientas **seguridad** botón de navegación jerárquica a la actividad anterior. En este ejemplo, el `DetailActivity` habilita el botón Inicio la **una** botón mediante la realización de los siguientes `SupportActionBar` llamadas al método: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Cuando el usuario se desplaza de `MainActivity` a `DetailActivity`, `DetailActivity` muestra un **seguridad** botón (flecha izquierda señalador) como se muestra en la captura de pantalla:

[![Captura de pantalla ejemplo de un botón izquierda arriba en la barra de herramientas](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png)

Al puntear en esto **una** botón hará que la aplicación volver a `MainActivity`. En una aplicación más compleja con varios niveles de jerarquía, puntee en este botón devolvería el usuario al siguiente nivel más alto en la aplicación en lugar de a la pantalla anterior. 



## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas del círculo (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de herramientas de AppCompat (ejemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
