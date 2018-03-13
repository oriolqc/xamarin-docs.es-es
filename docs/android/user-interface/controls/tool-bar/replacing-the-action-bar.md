---
title: Reemplazar la barra de acciones
ms.topic: article
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: e71c6ea816b8b732d21148db32fd9395732dd4c0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="replacing-the-action-bar"></a>Reemplazar la barra de acciones


## <a name="overview"></a>Información general

Uno de los más comunes se utiliza para la `Toolbar` consiste en reemplazar la barra de acción predeterminada con una personalizada `Toolbar` (cuando se crea un nuevo proyecto Android, usa la barra de acción predeterminada). Dado que el `Toolbar` ofrece la posibilidad de agregar logotipos de marca, títulos, elementos de menú, botones de navegación y vistas personalizadas incluso a la sección de la barra de aplicación de una actividad interfaz de usuario, ofrece una importante mejora por encima de la barra de acción predeterminada.

Para reemplazar la barra de acción predeterminada de una aplicación con un `Toolbar`: 

1.  Crear un nuevo tema personalizado y modificar las propiedades de la aplicación para que utilice el nuevo tema. 

2.  Deshabilitar la `windowActionBar` de atributo en el tema personalizado y habilitar la `windowNoTitle` atributo.

3.  Definir un diseño para el `Toolbar`.

4.  Incluir el `Toolbar` diseño de la actividad **Main.axml** archivo de diseño. 

5.  Agregue código a la actividad `OnCreate` método para buscar el `Toolbar` y llame a `SetActionBar` para instalar el `ToolBar` como la barra de acciones.

Las siguientes secciones explican con detalle este proceso. Se crea una aplicación sencilla y se reemplaza la barra de acción con un personalizada `Toolbar`. 



## <a name="start-an-app-project"></a>Iniciar un proyecto de aplicación

Crear un nuevo proyecto Android denominado **ToolbarFun** (consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) para obtener más información acerca de cómo crear un nuevo proyecto de Android). Después de crea este proyecto, establecer los niveles de API de Android de destino y los valores mínimo **Android 5.0 (API nivel 21 - círculo)**. Para obtener más información acerca de los niveles de versión de Android de configuración, consulte [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md). Cuando se compila y se ejecute la aplicación, tal como se muestra en esta captura de pantalla muestra la barra de acción predeterminada: 

[![Captura de pantalla de la barra de acción predeterminada](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>Crea un tema personalizado

Abra la **recursos/valores** directory y crear un nuevo archivo denominado **styles.xml**. Reemplace el contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

Este código XML define un tema personalizado nuevo llamado **mitema** que se basa en el **Theme.Material.Light.DarkActionBar** el tema en el círculo. El `windowNoTitle` atributo está establecido en `true` para ocultar la barra de título: 

```xml
<item name="android:windowNoTitle">true</item>
```

Para mostrar la barra de herramientas personalizada, el valor predeterminado `ActionBar` debe estar deshabilitada: 

```xml
<item name="android:windowActionBar">false</item>
```

Un verde aceituna `colorPrimary` configuración se utiliza para el color de fondo de la barra de herramientas: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

Editar **Properties/AndroidManifest.xml** y agregue el siguiente `android:theme` atribuir a la `<application>` elemento para que la aplicación usa el `MyTheme` tema personalizado: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Para obtener más información sobre cómo aplicar un tema personalizado a una aplicación, consulte [temas personalizados utilizando](~/android/user-interface/material-theme.md#customtheme). 



## <a name="define-a-toolbar-layout"></a>Definir un diseño de la barra de herramientas

En el **recursos/diseño** directorio, cree un nuevo archivo denominado **toolbar.xml**. Reemplace el contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

Este código XML define personalizado `Toolbar` que reemplaza a la barra de acción predeterminada. El alto mínimo de la `Toolbar` se establece en el tamaño de la barra de acción que lo reemplaza: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

El color de fondo de la `Toolbar` se establece en el color verde aceituna definido anteriormente en **styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partir del círculo, la `android:theme` atributo se puede usar para definir el estilo de una vista individual. El `ThemeOverlay.Material` temas introducidos en el círculo le permite superponer el valor predeterminado `Theme.Material` temas, sobrescribiendo los atributos pertinentes para hacerlas claro u oscuro. En este ejemplo, el `Toolbar` utiliza un tema oscuro para que su contenido es un color claro en: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Esta configuración se utiliza para que los elementos de menú contrastar con el color de fondo más oscuro.



## <a name="include-the-toolbar-layout"></a>Incluye el diseño de la barra de herramientas

Editar el archivo de diseño **Resources/layout/Main.axml** y reemplazar su contenido con el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <Button
        android:id="@+id/MyButton"
        android:layout_below="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello World, Click Me!" />
</RelativeLayout>
```

Este diseño incluye la `Toolbar` definido en **toolbar.xml** y utiliza un `RelativeLayout` para especificar que el `Toolbar` debe ponerse en la parte superior de la interfaz de usuario (encima del botón). 



## <a name="find-and-activate-the-toolbar"></a>Buscar y activar la barra de herramientas

Editar **MainActivity.cs** y agregue la siguiente instrucción using:

```csharp
using Android.Views;
```

Además, agregue las siguientes líneas de código al final de la `OnCreate` método:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Este código encuentra la `Toolbar` y llamadas `SetActionBar` para que la `Toolbar` vaya a realizar en las características predeterminadas de la barra de acción. El título de la barra de herramientas se cambia a **mi barra de herramientas**. Tal como se muestra en este ejemplo de código, el `ToolBar` puede hacer referencia directamente como una barra de acción. Compilar y ejecutar esta aplicación &ndash; personalizada `Toolbar` se muestra en lugar de la barra de acción predeterminada: 

[![Captura de pantalla de la barra de herramientas personalizada con el esquema de color verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Tenga en cuenta que la `Toolbar` estilo independientemente de la `Theme.Material.Light.DarkActionBar` tema que se aplica al resto de la aplicación. 


 
## <a name="add-menu-items"></a>Agregar elementos de menú 

En esta sección, los menús se agregan a la `Toolbar`. El área superior derecha de la `ToolBar` está reservado para los elementos de menú &ndash; cada elemento de menú (también denominado una *elemento de acción*) puede realizar una acción dentro de la actividad actual o puede realizar una acción en nombre de toda la aplicación. 

Para agregar los menús de la `Toolbar`: 

1.  Agregar iconos de menú (si es necesario) a la `mipmap-` carpetas de proyecto de la aplicación. Google Proporciona un conjunto de iconos del menú disponible en la [iconos Material](https://design.google.com/icons/) página. 

2.  Definir el contenido de los elementos de menú mediante la adición de un nuevo archivo de recursos de menú en **recursos/menú**. 

3.  Implemente el `OnCreateOptionsMenu` método de la actividad &ndash; este método aumenta los elementos de menú. 

4.  Implemente el `OnOptionsItemSelected` método de la actividad &ndash; este método realiza una acción cuando un elemento de menú que se derivan. 

Las secciones siguientes muestran el proceso detalladamente agregando **editar** y **guardar** elementos de menú a la personalizada `Toolbar`. 



### <a name="install-menu-icons"></a>Instalar iconos de menú

Continuar con la `ToolbarFun` aplicación de ejemplo, agregar iconos de menú para el proyecto de aplicación. Descargar [icons.zip de la barra de herramientas](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons.zip?raw=true) y descomprímalo. Copie el contenido de la *asignación MIP -* carpetas al proyecto *asignación MIP -* carpetas bajo **ToolbarFun/recursos** e incluya cada archivo de icono agregada en el proyecto.


### <a name="define-a-menu-resource"></a>Definir un recurso de menú

Crear un nuevo **menú** subdirectorio bajo **recursos**. En el **menú** subdirectorio, cree un nuevo archivo de recursos de menú denominado **top_menus.xml** y reemplazar su contenido con el siguiente código XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Este código XML crea tres elementos de menú:

-   Un **editar** elemento de menú que usa el `ic_action_content_create.png` icono (un lápiz). 

-   A **guardar** elemento de menú que usa el `ic_action_content_save.png` icono (un disquete). 

-   A **preferencias** elemento de menú que no tiene un icono.

El `showAsAction` atributos de la **editar** y **guardar** elementos de menú se establecen en `ifRoom` &ndash; esta configuración hace que estos elementos de menú que aparecen en la `Toolbar` si no existe espacio suficiente para que se muestren. El **preferencias** conjuntos de elementos de menú `showAsAction` a `never` &ndash; Esto hace que la **preferencias** menú aparezca en el *desbordamiento* menú (tres puntos verticales). 


### <a name="implement-oncreateoptionsmenu"></a>Implementar OnCreateOptionsMenu

Agregue el método siguiente a **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Llamadas Android el `OnCreateOptionsMenu` método para que la aplicación puede especificar el recurso de menú para una actividad. En este método, el **top_menus.xml** recursos se aumenten en el pasado `menu`. Este código hace que el nuevo **editar**, **guardar**, y **preferencias** elementos de menú que aparecen en la `Toolbar`. 



### <a name="implement-onoptionsitemselected"></a>Implementar OnOptionsItemSelected

Agregue el método siguiente a **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Cuando un usuario puntee en un elemento de menú, Android llama el `OnOptionsItemSelected` método y pasa el elemento de menú que se ha seleccionado. En este ejemplo, la implementación solo muestra una notificación del sistema para indicar qué elemento de menú se puntea. 

Compile y ejecute `ToolbarFun` para ver los nuevos elementos de menú en la barra de herramientas. La `Toolbar` muestra ahora tres iconos de menú tal como se muestra en esta captura de pantalla: 

[![Diagrama que muestra ubicaciones de editar, guardar y elementos de menú de desbordamiento](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Cuando un usuario derivaciones el **editar** elemento de menú, una notificación del sistema se muestra para indicar que el `OnOptionsItemSelected` se llamó el método: 

[![Captura de pantalla de notificaciones del sistema muestra al que se derivan editar elementos](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Cuando un usuario puntee en el menú de desbordamiento, el **preferencias** se muestra el elemento de menú. Normalmente, las acciones de menos común deben colocarse en el menú de desbordamiento &ndash; este ejemplo utiliza el menú de desbordamiento de **preferencias** porque no se utiliza con tanta frecuencia como **editar** y  **Guardar**: 

[![Elemento de menú de captura de pantalla de preferencias que aparece en el menú de desbordamiento](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Para obtener más información acerca de los menús de Android, consulte el desarrollador Android [menús](https://developer.android.com/guide/topics/ui/menus.html) tema. 
 



## <a name="related-links"></a>Vínculos relacionados

- [Barra de herramientas del círculo (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de herramientas de AppCompat (ejemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
